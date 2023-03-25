# 通过多个页面迭代 API

> 原文：<https://medium.com/hackernoon/iterating-through-apis-with-multiple-pages-bf9d4f8d029b>

任何从 API 中提取数据的人都知道它对于为特定主题提取大量数据是多么有用。为此，您可以使用以下代码:

```
all_characters = RestClient.get('[http://www.swapi.co/api/people/'](http://www.swapi.co/api/people/'))
character_hash = JSON.parse(all_characters)
```

在那个例子中，我们正在调用《星球大战》中的角色 [API](https://hackernoon.com/tagged/api) 。我们使用 rest client . get(([http://www.swapi.co/api/people/](http://www.swapi.co/api/people/')’)来实现这一点，我们将其设置为等于变量 all_characters。这将返回一个字符串，然后我们使用 JSON.parse 将它解析成一个可用的散列。

```
{"count"=>87,"next"=>"http://www.swapi.co/api/people/?page=2","previous"=>nil,"results"=>[{"name"=>"Luke Skywalker","height"=>"172","mass"=>"77","hair_color"=>"blond","skin_color"=>"fair","eye_color"=>"blue","birth_year"=>"19BBY","gender"=>"male","homeworld"=>"http://www.swapi.co/api/planets/1/","films"=>["http://www.swapi.co/api/films/2/","http://www.swapi.co/api/films/6/","http://www.swapi.co/api/films/3/","http://www.swapi.co/api/films/1/","http://www.swapi.co/api/films/7/"],"species"=>["http://www.swapi.co/api/species/1/"],"vehicles"=>["http://www.swapi.co/api/vehicles/14/", "http://www.swapi.co/api/vehicles/30/"],"starships"=>["http://www.swapi.co/api/starships/12/", "http://www.swapi.co/api/starships/22/"],"created"=>"2014-12-09T13:50:51.644000Z","edited"=>"2014-12-20T21:17:56.891000Z","url"=>"http://www.swapi.co/api/people/1/"},{"name"=>"C-3PO","height"=>"167","mass"=>"75","hair_color"=>"n/a","skin_color"=>"gold","eye_color"=>"yellow","birth_year"=>"112BBY","gender"=>"n/a","homeworld"=>"http://www.swapi.co/api/planets/1/","films"=>
```

我只包括了输出的前几行，但基本上它是一个散列，每个主要字符都有不同的信息。需要注意的一件重要事情是，即使我们提取了整个字符 API，每个[页面](https://hackernoon.com/tagged/page)也只显示了大约 10 个字符。换句话说，为了查看/检查所有角色的信息，你必须遍历多个页面。

我们如何做到这一点？如果您查看 character_hash，您会注意到其中一个键“next”显示下一页的 URL 是什么。如果我们可以设置 URL，让 RestClient 拉下一页，我们将能够循环(提示)每个页面，直到我们找到我们想要的。

出于我们实验室的目的，我们希望我们的方法接受一个字符的参数，并遍历散列来查找该字符，并返回该字符所在的电影的 URL 数组。结果代码是这样的:

```
def get_character_movies_from_api(character)

  all_characters = RestClient.get('[http://www.swapi.co/api/people/'](http://www.swapi.co/api/people/'))
  character_hash = JSON.parse(all_characters)while character_hash
    film_urls = character_hash["results"].find do |hash|
      hash["name"].downcase == character
    end
    if film_urls
      return film_urls["films"].map do |film|
        JSON.parse(RestClient.get(film))
      end
    end
    character_hash = character_hash["next"] ? JSON.parse(RestClient.get(character_hash["next"])) : nil
  end
end
```

我们实现了一个“while”循环，当 character_hash 不为 false 或 nil 时，它将继续运行这个块。它调用#find 方法来查找参数中传递的字符。如果找到了这个字符，它就获取得到的数组，并对其进行迭代以获得另一个电影数组。

一旦找到了字符，并对结果数组进行迭代，该方法就完成了。但是，如果在第一页找不到该字符，我们需要转到下一页。这就是下一行代码发挥作用的地方。如果 character_hash 中的“next”键有一个 URL，那么我们解析这个 URL 来获得下一页的 hash，并重复这个循环。如果“next”键的值是 nil，那么 character_hash 被设置为 nil，while 循环被中断。