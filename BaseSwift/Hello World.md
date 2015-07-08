Swift 笔记

最近许多文章都用到 Swift，学习一下 Swift 基础，避免看视频，博客时遇到麻烦。

开发环境：Xcode 6.4，知识点主要来源于[极客学院](http://www.jikexueyuan.com/path/ios/)的 iOS 视频教程和 Apple 的 [The Swift Programming Language](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105)。

Swift 的语法变化挺快的，下面如果编译时错了如果不是写错了那么可能语法已经更改，建议去 The Swift Programming Language 查看最新语法。还有一种可能就是编译器的问题，编译器对语法的要求很严格，导致语法正确也报错。

#基本语法

## Hello World

main.swift:

```

import Foundation

println("Hello, World!")

```

##变量和常量

变量

```
var a ＝ 1
a = 10
var b = c
```

常量，设定值后不允许再次修改值。

`let c = a + b `

## Swift 数据类型

定义变量

`var str ＝ "Hello "`

定义特定类型

``` 

var s:String = "World"

var i:Int = 100

var Words:String = "World"

```
> **说明：**一般来说不需要指定类型，Swift 会自动分析类型。

## 字符串连接

 
### 字符串相加

```
var str = "Hello "
str = str + "World"

println(str)

```

这种方式不能与其它类型直接连接

### 不同类型连接

```

var i = 200
var str = "Hello "
str = "\(str),Other Str,\(100),\(i)"

```

## 数组

不同类型可以放入同一个数组

`var array = ["Hello","World",100,2.3]`

声明空数组

`var empty = []`

只能存放特定类型的数组

`var arrStr =[String]()`

## 字典

类似 C++ 的 Map

创建一个字典

`var dict = ["name":"jikexueyuan" , "age" : "18"]`

动态添加字段

`dict["sex"] = "Female"`

取字段值

`println(dict["name"])`

## 循环语句

### 基本循环

```
var arr =[String]()

for index in 0...100{
	arr.append("Item \(index)")
}

println(arr)

```

### for in



```

//array 是一个数组

for value in array{
	println(value)
}

```


### while

```

var i = 0

//array 是一个数组

while i<arr.count {
	println(arr[i])
	i++
}

```

### 遍历字典

```

var dict = ["name" : "Hello","age" : "18"]

for(key,value) in dict{
	println("\(key),\(value)")
}

```
## 流程控制

```

for index in 0...100{
	if index%2==0 {
		println(index)
	}
	
}

//定义一个可选变量
var myName:String ?= "jike"

//如果可选变量为空，条件判断不执行。
if let name = myName {
	println("Hello \(name)")
}

```

## 函数

```

func sayHello(name:String){
	println("Hello \(name)")
}

sayHello("JiKe");

```

### 指定返回值类型

```

func getNums()->Int{
	return 2
}




```

### 返回多个值

```

func getNums()->(Int,Int){
	return (2,3)
}

var (a,b) = getNums()

println(a)


```

### 函数当变量使用

```

var fun = sayHello

fun("ZhangSan")

```

### 函数闭包

可以在函数内部写函数


## Swift 的面向对象

### 基本代码

```

class Hi{
	func sayHi(){
		println("Hello,JiKe")
	}
}

//类继承
class Hello:Hi{
	
	//如果有传入参数则使用传入的参数，没有则使用默认参数
	var _name:String ?= "jike"
	//构造方法	

	init(){
		println("init start")
		self._name = name
	}

	//重写方法
	override func sayHi(){
		
		
		println("Hi again \(self._name)")
	
		//执行父类方法
		
		super.sayHi()
	}
	
	//静态方法，类方法
	class func sayHello(){
		println("Hi Swift")
	}
}

var hi = Hi()
hi.sayHi()


var h = Hello(name:"ZhangsSan")
h.sayHi()

```

## 类的动态拓展

在不改变类的继承结构下拓展类的功能的一种方式

```

extension Hi(){
	func sayHaHa(){
	}
}

这时可以在 Hi 的例子中调用 sayHaha，并且 Hi 的所有继承的子类中也会拥有 Haha

```

## 协议

```

protpcol People{
	func getName()->String
}

class Man:People{
	func getName() -> String {
	
		return "ZhangSan"
	}
}

var m = Man()
println("Name is \(m.getName())")


```

## 命名空间

Swift 中并没有命名空间，通过类的嵌套可以实现命名空间的概念。

### 直接的写法

```

class com{
	class jike{
		class XueYuan{
			func sayHello(){
				println("Hello")
			}
		}
	}
}

```

### 使用拓展添加类到命名空间中

```

class com{
	class jike{
	}
}

extension com.jike{
	class Hello{
		func sayHello(){
			println("Hello jike")
		}
	}
}

extension com.jike{
	class World{
		func sayWorld(){
			println("Hello jike")
		}
	}
}


```
