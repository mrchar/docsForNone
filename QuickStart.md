# QuickStart

## HelloWorld
虽然HelloWorld并不能很好的展示None语言的特性，但是作为最简单的程序示例，使用HelloWorld程序已经成为一个不能免俗的过程。
```
. = require("builtin")
println("Hello World!")
```

第一行我们将builtin包引入到上下文中，builtin包中包含了我们要用到的println函数，然后使用println函数输出"Hello World!"字符串。

### 备注
在编程语言中引入builtin包是一个非常奇怪的行为，因为在其他语言中内建函数通常是默认引入的。None语言不希望如此，在没有引入包的情况下，程序只能使用关键字和运算符，没有任何内建函数占用命名空间。我不希望发生内建函数占用命名空间的事情，同时我也不希望发生像Golang中内建函数可以被重写这样的愚蠢的事情，所以干脆默认不引入内建函数，这样内建函数可以使用灵活的命名空间。

## 变量定义
None语言中使用new关键字定义变量
```
new a = 1
```
new关键字的语义是创建新的内存空间，然后将赋值号右侧的值拷贝到新创建的内存空间。这里的1是一个常量，这条语句会根据常量1的数据类型创建新的内存空间并进行初始化。
除了使用常量初始化新的内存空间，也可以使用变量初始化内存空间。
```
new a = 1
new b = a
```
上面的语句中，我们首先创建了变量a，然后使用变量a作为模板，创建了变量b。
另外，我们也可以为变量创建别名，使用两个不同的名字表示同一个变量。
```
new a = 1
b = a
```
第二行代码中没有new关键字，表示我们不会为b创建新的内存空间，也就是说b实际上仅仅是a的另外一个名字，b和a使用的是同一块内存空间。
那么
```
. = require("builtin")
new a = 1
b = a
b = 2
println(a, b)
```
将会打印出 2 2 的结果，因为修改b就是修改a。当然，没有人会在一个作用域中使用两个名字表示同一个变量，这样会给别人带来困扰，同时也不会有什么好处。
声明变量时也可以明确指定变量的数据类型。
```
new a: int = 1
```
":"后跟数据类型表示指定变量的数据类型。另外int实际上就是常量0，至于原因我们会在以后的文档中详细解释。所以你也可以结合new关键字声明变量。
```
new a = int
```
实际上就是定义一个值为0的int类型的变量。

## 常量定义
因为常量不能修改，所以不需要为常量创建新的内存空间，只需要为常量创建命名就可以定义新的变量。比如：
```
A = 1
```
就创建了一个值为1的常量，但是为了定义更加明确，不建议使用这种隐晦的定义方式，使用const关键字是更加推荐的方式。
```
const A = 1
```
就是定义了一个值为1的常量。

## 定义函数
```
. = require("builtin")

add = (arg1, arg2: int): int{
  return arg1 + arg2
}
a, b = 1, 1
c = add(a, b)
printf("%d + %d = %d", a, b, c)
```
上面的程序会打印出：1 + 1 = 2。  
```
. = require("builtin")
conv = (arg: int) {
  arg = 2
}
new a = 1
conv(a)
println(a)
```
上面的程序会打印出结果：2，因为函数conv的形参arg是实参的别名，在conv函数中对arg的修改实际上就是对实参a的修改，所以打印出的结果是修改后的a。
如果你不希望conv函数修改实参，那么在定义形参的时候应该用new关键字在函数的作用域内定义新的变量。
```
. = require("builtin")
conv = (new arg: int) {
  arg = 2
  println("arg:", arg)
}
new a = 1
conv(a)
println("a:", a)
```
因为我们使用new关键字申请了新的内存空间，所以参数传递的时候会把a的值拷贝到conv函数内部，conv函数不能修改外部的变量，打印出的a的值还是1。

## 数据结构
None最大限度的兼容JSON格式的文档，定义数据结构可以直接使用JSON定义。
```
Person = {
  name: "Bob",
  age: 20
}
```
也可以像下面这样定义：
```
Person: Object = {
  name: "Bob",
  age: 20
}
```
这样我们就定义了一个Person类型，Person类型的默认值为{name:"Bob", arg: 20}。Person本身是一个常量，同时也是一个数据类型，Person的类型为Object，我们可以使用Person作为模板创建新的Person类型的变量。
```
Person = {
  name: "Bob",
  age: 20
}
new person: Person = {
  name: "Alice",
  age: 18
}
```
### 备注
我不希望None语言创建实例前都需要先定义类，所有的变量或者常量都可以作为模板创建新的变量。所以定义变量和常量就是定义新的数据类型。上面创建了一个名称为Person的常量，这个常量同时就是一个新的数据类型。

## 包管理
None语言借鉴gomod的版本管理方式，使用包文件的摘要作为版本指纹，使用版本管理系统的Tag作为版本号。
跟其他大多数语言不同的是，None语言的包可以选择是否创建多实例。
比如我们分别在a包和b包中分别引入c包，然后先后打印c包中的变量。

c包
```
new param = 1
```

a包
```
. = require("builtin")
c = require("c")
println(c.param)
c.param = 2
```

b包
```
. = require("builtin")
c = require("c")
println(c.param)
```

会看到b中打印的c.param已经变成a中修改后的2。跟大多数语言一样，a包和b包中引入的c实际上是同一个实例。

如果使用new关键字引入c包。

c包
```
new param = 1
```

a包
```
. = require("builtin")
c = require("c")
println(c.param)
c.param = 2
```

b包
```
. = require("builtin")
new c = require("c")
println(c.param)
```
在b包中引入c包时创建了新的实例，所以b包打印出的将是一个独立的结果，而不是a中修改后的结果。

并且包可以作为参数传递。