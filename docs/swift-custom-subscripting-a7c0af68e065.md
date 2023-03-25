# Swift 定制[订购]

> 原文：<https://medium.com/hackernoon/swift-custom-subscripting-a7c0af68e065>

本周我们将暂停谈论 ReaktiveKit 和 Bond。在构建我们自己的事件和可观察类时，我陷入了一个类似兔子洞的境地，所以本周我们将稍微偏离主题，谈谈在 [Swift](https://hackernoon.com/tagged/swift) 中订阅的[。](https://hackernoon.com/tagged/subscripting)

我们一直在 Swift 中使用下标，通常是在数组或字典中。它们看起来像这样:

```
someArray[2] = "Some value"
someDictionary["some key"] = "Some value
```

你明白了。我们用数组和字典做各种各样的事情。但是最棒的是，我们可以给自己的自定义类和结构添加下标。同样的方式，我们可以为我们的自定义类型提供一个初始化器，我们可以提供一个下标。这里有一个非常简单的例子:

```
struct Group { subscript(index: Int) -> Int {
      get {
         return index
      }
   }
}let justiceLeague = Group()
print(justiceLeague[3]) //prints "3"
print(justiceLeague[2]) //prints "2"
```

![](img/b7bb6da4d893ab210a9235bb8b45c69d.png)

这是最基本的工作原理。让我们把它放大一点，展示一下能够下标是如何有用的。在我不久前发布的一个应用程序中，我有一个用来表示音符的网格:

![](img/048147c5399d7333402d64651fa9c97b.png)

[BlipBloop](https://itunes.apple.com/us/app/blipbloop/id1237810871?mt=8)

每个按钮代表一个音符，而音符又由一个结构来表示。我们称这个结构为“音符”下面是 Note 的一个简单实现:

```
struct Note {
   let pitch: Int
   let isOn: Bool static let empty = Note(pitch: 0, isOn: false)
}
```

现在，如果有一种方法可以用下标的方式来表示音符网格，那就太棒了。所以任何时候我们想改变这个网格上的一个音符，我们可以这样做:

```
grid[2, 3] = Note(pitch: 67, isOn: true)
```

下面是一个可以代表我们网格的自定义类的样子:

```
struct NoteMatrix{
   let rows: Int
   let columns: Int
   var grid = [Note]() init(rows: Int, columns: Int) {
      self.rows = rows
      self.columns = columns
      grid = Array(repeating: Note.empty, count: columns * rows)
   } subscript(row: Int, column: Int) -> Note {
      get {
         return grid[(row * columns) + column]
      }
      set(newValue) {
         grid[(row * columns) + column] = newValue
      }
   }
}
```

现在我们可以这样做:

```
var score = NoteMatrix(rows: 4, columns: 4)
score[2, 3] = Note(pitch: 67, isOn: true)print(score[2, 3].pitch) //prints "67"
```

嘿，你看看那个。我们还可以在下标的“get-set”部分添加一个 assert 语句，如果超出了行数和列数，该语句将使所有内容崩溃:

```
assert(row >= 0 && row < rows && column >= 0 && column < columns, "Index out of range")
```

本质上，我们有创建集合类型的构件。我想看看我是否能让这个矩阵类和泛型一起工作，所以我拼凑了一些东西，它工作起来没有任何错误。虽然不太好看。如果你有更好的方法，请在评论区告诉我。在这里…呃…在这里:

```
struct Matrix<T>{
   let rows: Int
   let columns: Int
   var grid = [Int: [Int: T]]() init(rows: Int, columns: Int) {
      self.rows = rows
      self.columns = columns
   } subscript(row: Int, column: Int) -> T? {
      get {
         assert(indexIsValid(row: row, column: column), "Index out of range")
         if let gridRow = grid[row] {
            if let output = gridRow[column] {
               return output
            } else {
               return nil
            }
         }else {
            return nil
         }
      } set(newValue) {
      assert(indexIsValid(row: row, column: column), "Index out of range")
         if let newValue = newValue {
            grid[row] = [column: newValue]
         }
      }
   }

   func indexIsValid(row: Int, column: Int) -> Bool {
      return row >= 0 && row < rows && column >= 0 && column < columns
   }}struct Note {
   let pitch: Int
   let isOn: Bool
}var noteGrid = Matrix<Note>(rows: 4, columns: 4)noteGrid[1, 1] = Note(pitch: 67, isOn: true)print(noteGrid[1, 1]?.pitch) //prints "67"
```

太恶心了。它肯定需要返工。但是现在我们有了一个通用的矩阵，可以用于我们的任何自定义类型。说真的，如果你有更好的方法，请告诉我。我有一种感觉，要有一种在自定义集合类型中存储值的方法，我必须更进一步。但是，无论如何。现在你可以看到订阅是如何工作的了！

![](img/f0372ed445d70bfe90670a71765bf06c.png)