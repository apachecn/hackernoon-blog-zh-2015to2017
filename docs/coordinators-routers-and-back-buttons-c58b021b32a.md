# 协调器、路由器和后退按钮

> 原文：<https://medium.com/hackernoon/coordinators-routers-and-back-buttons-c58b021b32a>

如果你偶然发现了这篇文章，你可能会遇到和我在和流量协调员一起工作时遇到的同样的问题。如果你是协调员的新手，我建议你阅读 Soroush Khanlou 的博客。这项工作是我理解协调器的基础，但在尝试实现我自己的协调器时，我在处理后退按钮时遇到了一个巨大的缺陷。

**水平流与垂直流**

子协调员可以代表垂直或水平流程。

您提供的子协调器是一个**垂直流**，当它完成时，您解除它并释放协调器。垂直流的一个例子是身份验证流，它以模式方式呈现，仅在身份验证被取消或用户通过登录或注册进行身份验证时才完成。

您推入的子协调器是一个**水平流**，当它完成时(从一个动作或后退按钮),它从导航控制器堆栈中弹出并被释放。为此，父协调器和子协调器需要共享同一个导航控制器。这个案例是在我进入个人资料页面时出现的。在这个 profile 页面上有许多其他的子流，所以我不能简单地实例化一个新的 profile view 控制器，并在我希望的时候推送到它。我需要一个可重用的协调器，我可以推动它。

实现这一点需要传递一个对 UINavigationController 的引用和大量处理导航控制器委托的混乱代码，以确定 back 按钮何时被按下，以便您可以释放子协调器。我开始寻找一种更好的方式，我遇到了 Andrey Panov 的协调器实现(T9 ),它包含了架构的一个新组件:路由器(T10)。您也应该阅读一下，因为为了简洁起见，我不会涵盖所有内容。

路由器是一个非常简单的概念。它是一个封装了 UINavigationController 的类，您可以在协调器之间传递它。它处理物理导航，而协调器处理流逻辑。每个水平流有一个路由器，并且可以通过依赖注入由水平流的子协调器共享。每当你有一个新的垂直流(通常是模态地呈现一个流)时，这将需要实例化一个新的路由器。

当使用 Khanlou 或 Andrey Panov 的水平流方法时，后退按钮是最大的痛苦。Khanlou 甚至有一整篇关于后退按钮和协调按钮一起使用的后续文章。我认为这些建议的解决方案是可以接受的，但仍不理想。

第一个建议是创建一个导航控制器类，它是 UIViewController 的子类，目的是封装 Navigation Controller，符合 UINavigationControllerDelegate，并维护从视图控制器到协调器的映射，以便在弹出视图控制器时可以释放协调器。这看起来像是子类化 UITabBarController，以避免将我们的应用程序的设置逻辑放在 AppDelegate 中。有用吗？是的，但是感觉不对。他继续说“我主要关心的是 NavigationController 类，它是一个视图控制器，知道并必须处理协调器”。第二种方法是将协调器扩展为导航控制器委托，然后在该委托方法触发时处理解除分配。这样做的最大缺点是，没有办法将导航控制器委托的责任移交给后续的子协调器。如果您推送到一个子流，然后推送到另一个流，第一个流将接收两个流的 pop 事件。我不确定这种方法是否适用于一层以上的深度。

我认为将这两种方法结合起来会更好。如果我们扩展我们的路由器类以符合 UINavigationControllerDelegate，那么路由器可以处理它所包装的导航控制器的所有事件，并将在后退按钮事件上做什么的责任委托给最初推动该协调器的协调器，会怎么样？

在我看来，理想的解决方案应该是…

1.允许我们像对待垂直流中的取消按钮一样对待水平流中的后退按钮

2.避免用流逻辑(协调器)污染导航(路由器)

3.将按下后退按钮时要做的事情(子协调器的解除分配)委托给父协调器

4.为展示和推送协调器和视图控制器提供一个公共界面

5.允许我们轻松地推进到后续的水平流程

在我看来应该是这样的:

```
let coordinator = ProfileCoordinator(router: router, store: store, profile: profile)addChild(coordinator)router.push(coordinator) { [weak self, weak coordinator] in
    // This executes when the back button is pressed
    self?.removeChild(coordinator)
}
coordinator.start()
```

对于协调者和视图控制器，我们应该能够与路由器进行相同的交互:

```
let viewController = UIViewController()
router.push(viewController) {
    // This executes when the back button is pressed
}
```

因此，我们的 push 函数需要采用一个通用类型，我们的路由器需要跟踪回调，以便在 back 按钮被按下时执行它们。

我们常见的类型是可展示的协议:

```
public protocol Presentable {
    func toPresentable() -> UIViewController
}// UIViewController is already a presentable type
extension UIViewController: Presentable {
    public func toPresentable() -> UIViewController {
        return self
    }
}
```

现在我们的路由器接口和实现(这是 Andrey Panov 实现的修改版本，提供后退按钮支持):

```
import UIKitpublic protocol RouterType: class, Presentable {
    var navigationController: UINavigationController { get }
    var rootViewController: UIViewController { get }
    func present(_ module: Presentable, animated: Bool)
    func push(_ module: Presentable, animated: Bool, completion: (() -> Void)?)
    func popModule(animated: Bool)
    func dismissModule(animated: Bool, completion: (() -> Void)?)
    func setRootModule(_ module: Presentable, hideBar: Bool)
    func popToRootModule(animated: Bool)
}final public class Router: NSObject, RouterType, UINavigationControllerDelegate { private var completions: [UIViewController : () -> Void]
    public var rootViewController: UIViewController...
    public unowned let navigationController: UINavigationController public init(navigationController: UINavigationController) {
        self.navigationController = navigationController
        self.completions = [:]
        super.init()
        self.navigationController.delegate = self
    } public func toPresentable() -> UIViewController {
        return navigationController
    } public func present(_ module: Presentable, animated: Bool) {
        guard let controller = module.toPresentable() else { 
            return
        } navigationController.present(controller, animated: animated, completion: nil)
    } public func dismissModule(animated: Bool, completion: (() -> Void)?) {
        navigationController.dismiss(animated: animated, completion: completion)
} public func push(_ module: Presentable, animated: Bool = true, completion: (() -> Void)? = nil) { // Avoid pushing UINavigationController onto stack
        guard let controller = module.toPresentable(), 
            controller is UINavigationController == false else {
            return
        } if let completion = completion {
            completions[controller] = completion
        } navigationController.pushViewController(controller, animated: animated)
    } public func popModule(animated: Bool = true)  {

        if let controller = navigationController.popViewController(animated: animated) {
            runCompletion(for: controller)
        }
    } public func setRootModule(_ module: Presentable, hideBar: Bool){
        guard let controller = module.toPresentable() else { 
            return
        } navigationController.setViewControllers([controller], animated: false)
        navigationController.isNavigationBarHidden = hideBar
    } public func popToRootModule(animated: Bool) {
        if let controllers = navigationController.popToRootViewController(animated: animated) {
            controllers.forEach { runCompletion(for: $0) }
        }
    } fileprivate func runCompletion(for controller: UIViewController) {
        guard let completion = completions[controller] else {
            return
        } completion()
        completions.removeValue(forKey: controller)
    } // MARK: UINavigationControllerDelegate
    public func navigationController(_ navigationController: UINavigationController, didShow viewController: UIViewController, animated: Bool) {

        // Ensure the view controller is popping
        guard let poppedViewController = navigationController.transitionCoordinator?.viewController(forKey: .from), !navigationController.viewControllers.contains(poppedViewController) else {
             return
        } runCompletion(for: poppedViewController)
    }
}
```

我们的路由器应该能够执行所有可能的导航操作。它还必须充当导航控制器的委托，这样它就可以拦截 back 按钮的按下，并为弹出的视图控制器运行相应的完成处理程序。当一个可呈现的类型被推送时，我们使用*module . top Presentable()*访问要被推送的视图控制器，并将完成处理程序存储在一个字典中，关键字是视图控制器。当从后退按钮弹出一个视图控制器时，导航控制器委托函数确定哪个视图控制器被弹出，并执行相应的完成处理程序。

现在我们的协调器接口和实现:

```
public protocol Coordinatable: class, Presentable {
    var router: RouterType { get }
    var onCompletion: (() -> Void)? { get set }
    func start()
}open class Coordinator: NSObject, Coordinatable {

    public var childCoordinators: [Coordinator] = []
    public var router: Router public init(router: Router) {
        self.router = router
        super.init()
    } open var onCompletion: (() -> Void)? open func start() {} public func addChild(_ coordinator: Coordinator) {
        childCoordinators.append(coordinator)
    } public func removeChild(_ coordinator: Coordinator) {
        if let coordinator = coordinator, 
            let index = childCoordinators.index(of: coordinator) {
            childCoordinators.remove(at: index)
        }
    } // Make this function open so we can override it in a different module
    open func toPresentable() -> UIViewController {
        return router.toPresentable()
    }
}
```

我们的协调者必须实现 Presentable，这样路由器才能呈现它。这个默认实现将返回路由器的可显示的表单，该表单将返回路由器的底层导航控制器。这在为新的垂直流程提供协调者时非常有用，但是我们不能推动这样的协调者。因此，为了推动水平流的子协调器，我们必须覆盖 *toPresentable()* 来给我们一个视图控制器实例，它将在我们调用 *router.push(coordinator)* 时工作。注意:在我们的路由器中，我们要确保我们推送的视图控制器不是 UINavigationController。

因此，我们希望用于水平流的协调器的子类可能如下所示:

```
import Foundationopen class ProfileCoordinator: Coordinator { fileprivate let store: StoreType
    fileprivate let profile: Profile lazy var viewController: ProfileViewController = {
        let viewModel = LocationProfileViewModel(profile: profile)
        return ProfileViewController(viewModel: viewModel)
    }() public init(router: RouterType, store: StoreType, profile: Profile) {
        self.store = store
        self.profile = profile
        super.init(router: router)
    } open override func start() {} open override func toPresentable() -> UIViewController {
        return viewController
    }
}
```

瞧啊。我们可以从父协调器中推出这个子协调器，并轻松地解除分配:

```
let coordinator = ProfileCoordinator(router: router, store: store, profile: profile)addChild(coordinator)router.push(coordinator) { [weak self, weak coordinator] in
    // This executes when the back button is pressed
    self?.removeChild(coordinator)
}
coordinator.start()
```

希望这有所帮助。如果您对协调员的回调使用闭包而不是委托，那么这个解决方案将会工作得最好。欢迎任何反馈、问题或批评。

查看[坐标工具包](https://github.com/imaccallum/CoordinatorKit)进行演示。