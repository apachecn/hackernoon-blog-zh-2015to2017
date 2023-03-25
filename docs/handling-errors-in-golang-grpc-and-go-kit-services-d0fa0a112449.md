# 处理 gRPC 和 go-kit 服务中的错误

> 原文：<https://medium.com/hackernoon/handling-errors-in-golang-grpc-and-go-kit-services-d0fa0a112449>

![](img/87b0973e9af1ccdb50f32adb6469f3d4.png)

如果您查找如何处理 gRPC/go-kit 服务中的自定义错误，您可能会发现一些粗略的说明，但没有系统。这是一个令人沮丧的遗漏，因为自定义错误在 Go 项目中是有用且常见的。这是我的尝试。

1.  在 protobuf 中定义一条错误消息，包含服务内部和服务之间所需的字段/数据。
2.  为生成的错误实现 Go 的错误接口，这样它就可以像 Go 中的其他错误一样被传递。
3.  支持堆栈跟踪，以便您可以看到错误的来源并了解它是如何产生的。
4.  在响应的字段中响应错误，从而向其他服务公开错误。

# 在 protobuf 中定义一个有用的错误消息

什么是有用的错误？首先，我们的错误需要一条类似“找不到用户”的消息和一个类似 404 的代码。此外，嵌套错误适用于验证之类的情况，在这种情况下，错误由下属组成，消息可能类似于带有嵌套错误的“无效用户”{“email”:“必须设置”，“[密码](https://hackernoon.com/tagged/password)”:“必须更强”}。一段字符串用于捕捉所有其他细节——例如，如果您想要一个本地化的、面向用户的消息；把它放在细节里。

```
// example.proto

message Error {
  string message = 1;
  int32 code = 2;
  map<string, string> nested_errors = 3;
  repeated string details = 4;
  bytes stack = 5 [(gogoproto.customtype) = "Stack"];
}
```

# 实现错误接口

通过实现 error 接口，您可以像处理 writing Go 中的任何其他错误一样传递和使用错误。您将希望您的函数也将您的自定义错误作为错误接口返回，否则您将遇到这种令人困惑的行为:[为什么我的 nil 错误值不等于 nil？](https://golang.org/doc/faq#nil_error)

> 对于返回错误的函数来说，最好总是在它们的签名中使用`error`类型(就像我们上面做的那样),而不是一个具体的类型，比如`*MyError`,以帮助保证错误被正确地创建。作为一个例子，`[os.Open](https://golang.org/pkg/os/#Open)`返回一个`error`，即使如果不是`nil`，它总是具体类型`[*os.PathError](https://golang.org/pkg/os/#PathError)`。

我在网上看过一些意见，大部分来自谷歌阵营，他们认为编译后的 protobuf 代码应该与你自己手写的代码分开保存，这样这个 API 就不可能在 Go 中使用。相反，您应该在 protobuf 中定义一个错误，在您的代码中定义一个错误，并根据需要将它们相互转换。但是在实践中，手写代码和 protobufs 一起并没有造成问题，并且有利于像这样的 API 和其他有用的 API，比如用于通常检索的嵌套字段的 getters。所以我说继续吧。

```
// error.go

// assert Error implements the error interface.
var _ error = &Error{}

// Error implements the error interface.
func (e *Error) Error() string {
	b := new(bytes.Buffer)
	e.printStack(b)
	pad(b, ": ")
	b.WriteString(e.Message)
	if b.Len() == 0 {
		return "no error"
	}
	return b.String()
}
```

# 支持堆栈跟踪

堆栈跟踪可以很容易地找到错误的来源，并了解它们是如何产生的。

```
/path/main.go:20: github.com/travisjeffery/example.c:
	/path/main.go:16: ...b:
	/path/main.go:12: ...a: shit's on fire, yo
```

我使用 gogo 的 protobuf 分支来添加自定义类型的堆栈字段，官方的 [Golang](https://hackernoon.com/tagged/golang) protobuf 不支持这种类型。公共字段很烦人，我不希望它的内容被封送/解封——前者我无能为力，因为 protobuf 对私有字段很恼火，后者我可以通过实现 nop 的封送器/解封器接口来解决。

```
// stack.go

type Stack struct {
	Callers []uintptr
}

func (t Stack) Marshal() ([]byte, error) {
	return nil, nil
}

func (t *Stack) MarshalTo(data []byte) (n int, err error) {
	return 0, nil
}

func (t *Stack) Unmarshal(data []byte) error {
	return nil
}

func (t Stack) MarshalJSON() ([]byte, error) {
	return []byte(`null`), nil
}

func (t *Stack) UnmarshalJSON(data []byte) error {
	return nil
}

func (t *Stack) Size() int {
	return 0
}
```

我从 [upspin](https://github.com/upspin/upspin/blob/master/errors/errors.go) 那里窃取了[填充和打印堆栈跟踪](https://github.com/travisjeffery/grpc-go-kit-error-example/blob/master/error.go#L67-L163)的代码，反正大部分都是这样。当用 WrapErr 和 E 函数创建错误并用 Error()函数打印错误时，堆栈被填充。如果您想要一个没有堆栈跟踪的错误，那么您可以自己实例化一个。

WrapErr 类似于戴夫·切尼的[错误。包装](https://github.com/pkg/errors/blob/master/errors.go#L180)，接受一个错误并用消息包装它。我在代码的最底层使用 WrapErr 来注释由 stdlib 或第三方 pkg 返回的错误。E 接受各种类型的参数列表，并实例化相应的错误，例如 E(“找不到用户”，404)会将字符串与错误消息匹配，将 int 与错误代码匹配。

```
// error.go

// WrapErr returns an Error for the given error and msg.
func WrapErr(err error, msg string) error {
	if err == nil {
		return nil
	}
	e := &Error{Message: fmt.Sprintf("%s: %s", msg, err.Error())}
	e.populateStack()
	return e
}

// E is a useful func for instantiating Errors.
func E(args ...interface{}) error {
	if len(args) == 0 {
		panic("call to E with no arguments")
	}
	e := &Error{}
	b := new(bytes.Buffer)
	for _, arg := range args {
		switch arg := arg.(type) {
		case string:
			pad(b, ": ")
			b.WriteString(arg)
		case int32:
			e.Code = arg
		case int:
			e.Code = int32(arg)
		case error:
			pad(b, ": ")
			b.WriteString(arg.Error())
		}
	}
	e.Message = b.String()
	e.populateStack()
	return e
}
```

# 用错误字段响应

通过在每个响应上放置一个错误字段，将错误公开给其他服务。在服务的边缘，即端点，错误的类型被断言并在响应中设置。如果您有一个 HTTP 服务作为 gRPC 服务的网关，那么您可以使用 RPC 的错误代码来设置 HTTP 响应的状态代码。

```
// example.proto

service Users {
  rpc GetUser (GetUserRequest) returns (GetUserResponse) {}
}

message GetUserResponse {
  User user = 1;
  Error error = 2;
}

// endpoints.go

func makeGetUserEndpoint(userService user.Service) endpoint.Endpoint {
    return func(ctx context.Context, request interface{}) (interface{}, error) {
        req := request.(*GetUserRequest)
        user, err := userService.GetUser(req.User)
        if err != nil {
            return &GetUserResponse{Error: err.(*Error)}, nil
        }
        return &GetUserResponse{User: user}, nil
    }
}

// transport.go

r.Methods("GET").Handle("/users/{id}", httptransport.NewServer(
    endpoints.GetUserEndpoint,
    decodeGetUserRequest,
    encodeResponse,
    options...,
))

type errorer interface {
    GetError() *example.Error
}

func encodeResponse(ctx context.Context, w http.ResponseWriter, response interface{}) error {
    if e, ok := response.(errorer); ok {
        if err := e.GetError(); err != nil {
            w.WriteHeader(int(err.Code))
        }
    }
    w.Header().Set("Content-Type", "application/json; charset=utf-8")
    return json.NewEncoder(w).Encode(response)
}
```

# 一起看

我已经将一个[例子推送到 GitHub](https://github.com/travisjeffery/grpc-go-kit-error-example) 上，这样你就可以检查所有的东西了。

这个工作流程很棒，但是如果你有更好的意见，我想听听。

# 为什么不元数据/返回错误给 gRPC

我尝试编写一个 API，将错误返回给 gRPC，而不是设置在响应中。服务器上的中间件将把错误编码成[元数据](https://github.com/grpc/grpc-go/blob/master/Documentation/grpc-metadata.md)，客户机上的中间件将解码错误并返回，使 gRPC 调用完全像本地函数调用一样工作。

```
// example.protoservice Users {
  rpc GetUser (GetUserRequest) returns (GetUserResponse) {}
}message GetUserResponse {
  User user = 1;
  // Error's gone
}// endpoints.gofunc makeGetUserEndpoint(userService user.Service) endpoint.Endpoint {
    return func(ctx context.Context, request interface{}) (interface{}, error) {
        req := request.(*GetUserRequest)
        user, err := userService.GetUser(req.User)
        if err != nil {
            return nil, err
        }
        return &GetUserResponse{User: user}, nil
    }
}// client.go
resp, err := usersClient.GetUser(ctx, &GetUserRequest{Email: "email@example.com"})
if err != nil {
    ...
}
```

问题在于 [go-kit 没有给你机会将错误编码到上下文](https://github.com/go-kit/kit/blob/0c52d4024a04395dabac42b65e5825d08d3335df/transport/grpc/server.go#L91-L95)中。如果您的端点返回一个错误，go-kit 将直接将其发送给 gRPC，gRPC 将只知道获取错误的 error()字符串，而忽略其余的数据。不支持这一点的缺点是您的客户端在发出请求时必须检查两个错误，返回的错误和请求错误。我更希望 go-kit 支持它，但它似乎是[不会发生。](https://github.com/go-kit/kit/issues/283)

–

请在 [@travisjeffery](https://twitter.com/travisjeffery) 问好。

击中要害👏如果你觉得这个有用，分享一下。

谢谢你的阅读。