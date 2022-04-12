



# Go

## 变量初始化

如果用var则可以在func外面，如果用:=则要在func里面

```go
package main

import "fmt"
vac asd string=“asd"
func main() {
	asd := "asda"
	fmt.Println(asd)
}
```

**使用冒号等于号时，*要声明的变量必须是新变量*。**

*你能在函数内使用冒号等于号*。 在声明函数外的变量时，必须使用 `var` 关键字执行此操作。

声明常量

例如，你可以按如下所示声明常量：

Go复制

```go
const HTTPStatusOK = 200
```

与变量一样，Go 可以通过分配给常量的值推断出类型。 在 Go 中，常量名称通常以混合大小写字母或全部大写字母书写。

如果需要在一个块中声明多个常量，可以按如下所示执行：

Go复制

```go
const (
    StatusOK              = 0
    StatusConnectionReset = 1
    StatusOtherError      = 2
)

```
不能使用冒号等于号来声明常量。 如果采用这种方式，Go 会发出警告。

如果在main声明了变量但未使用，Go 会抛出错误

### 数据类型

#### 整型

```go
var integer8 int8 = 127
var integer16 int16 = 32767
var integer32 int32 = 2147483647
var integer64 int64 = 9223372036854775807
fmt.Println(integer8, integer16, integer32, integer64)
```

大多数情况下，你将使用 `int`，但需要了解其他整数类型，因为在 Go 中，`int` 与 `int32` 不同，即使整数的自然大小为 32 位也是如此。 **换句话说，需要强制转换时，你需要进行显式转换。** **如果尝试在不同类型之间执行数学运算，将会出现错误。**

在学习 Go 过程中，你可能会收到有关 [runes](https://www.geeksforgeeks.org/rune-in-golang/) 的信息。 `rune` 只是 `int32` 数据类型的别名。 它用于表示 Unicode 字符（或 Unicode 码位）。 例如，假设有以下代码：

Go复制

```go
rune := 'G'
fmt.Println(rune)
```

运行前面的代码片段时，你可能会在命令提示符下看到程序打印符 `G`。 不过，你还会看到数字 `71`，它表示 `G` 的 Unicode 字符。 

### 浮点型

```go
package main

import (
    "fmt"
    "math"
)    

func main() {
    fmt.Println(math.MaxFloat32, math.MaxFloat64)
}

```
在 Go 中，如果你不对变量初始化，所有数据类型都有默认值。 此功能非常方便，因为在使用之前，你无需检查变量是否已初始化。

类型转换可参考c

用strconv库也可实现

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	i, _ := strconv.Atoi("-42")//下划线 (_) 用作变量的名称。 在 Go 中，这意味着我们不会使用该变量的值，而是要将其忽略。
	s := strconv.Itoa(-42)
	fmt.Println(i, s)
}

```

转换的原理

```go
s := "128"
n := 0
for i := 0; i < len(s); i++ {
    n = 10 * n  + s[i] 
}  
```

字符串转为整型

```go
func ParseInt(s string, base int, bitSize int) (i int64, err error)
func ParseUint(s string, base int, bitSize int) (n uint64, err error)
func Atoi(s string) (i int, err error)
```

其中，Atoi 是 ParseInt 的便捷版，内部通过调用 ParseInt(s, 10, 0) 来实现的；ParseInt 转为有符号整型；ParseUint 转为无符号整型，着重介绍 ParseInt。

参数 base 代表字符串按照给定的进制进行解释。一般的，base 的取值为 2~36，如果 base 的值为 0，则会根据字符串的前缀来确定 base 的值："0x" 表示 16 进制； "0" 表示 8 进制；否则就是 10 进制。

参数 bitSize 表示的是整数取值范围，或者说整数的具体类型。取值 0、8、16、32 和 64 分别代表 int、int8、int16、int32 和 int64。

```go
n, err := strconv.ParseInt("128", 10, 8)
```

在 ParseInt/ParseUint 的实现中，如果字符串表示的整数超过了 bitSize 参数能够表示的范围，则会返回 ErrRange，同时会返回 bitSize 能够表示的最大或最小值。因此，这里的 n 是 127。

另外，ParseInt 返回的是 int64，这是为了能够容纳所有的整型，在实际使用中，可以根据传递的 bitSize，然后将结果转为实际需要的类型。

*别想着通过 string(65) 这种方式将整数转为字符串，这样实际上得到的会是 ASCCII 值为 65 的字符，即 'A'。*



### rune

```go
  // 统计字符串中中文个数 
res := []rune(s2)
  reslen := len(res)
  count := 0
  for i := 0; i < reslen; i++ {
    if res[i] > 255 {
      count++
    }
  }
  fmt.Printf("字符串:%s (Length = %d),一共有 %d 个中文字符", s2, reslen, count)
} 
```



## 指针

描述: Go 语言中的指针区别于C/C++中的指针，Go语言中的指针`不能进行偏移和运算`是`安全指针`。

Go 语言中三个重要概念: `指针地址`、`指针类型`以及`指针取值`。

简单回顾: 任何程序数据载入内存后，在内存都有他们的地址这就是指针。而为了保存一个数据在内存中的地址，我们就需要指针变量。

比如，“永远不要高估自己”这句话是我的座右铭，我想把它写入程序中，程序一启动这句话是要加载到内存（假设内存地址0x123456），我在程序中把这段话赋值给变量A，把内存地址赋值给变量B。这时候变量B就是一个`指针变量`, 通过变量A和变量B都能找到我的座右铭。

Go语言中的指针操作非常简单，我们只需要记住两个符号：`&（取地址）` 和 `*（根据地址取值）`。



### 1.指针地址

描述: 每个变量在运行时都拥有一个地址，该地址代表变量在内存中的位置。

Go语言中使用`&字符放在变量前面`对变量进行`“取地址”`操作。

取变量指针(地址)的语法如下：

```
ptr := &v    // v的类型为T

// # 参数
// v:代表被取地址的变量，类型为T
// ptr:用于接收地址的变量，ptr的类型就为*T，称做T的指针类型。*代表指针。
```



### 2.指针类型

描述: Go语言中的值类型`（int、float、bool、string、array、struct）`都有对应的指针类型，如：`*int、*int64、*string`等。

简单示例:

```
func main() {
	a := 10
	b := &a
	fmt.Printf("a:%d ptr:%p\n", a, &a)             // a:10 ptr:0xc00001a078 (指针地址)
	fmt.Printf("*b:%d ptr:%p type:%T\n",*b, b, b)  // b:10 ptr:0xc00001a078 type:*int (指针类型)
	fmt.Printf("&b ptr:%p ",b)                     // &b ptr:0xc00000e018
}
```

为了更好的理解指针地址，我们来看一下`b := &a`的图示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/f4oz4PGldsqLIKicRs8InEzPmHzaKUSYibFhJicrA2AF6BZ5Ipes5xLOzOzY9mB34Umr5fbSI1vIzOeSbiaISHJ23g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



### 3.指针取值

描述: 在对普通变量使用&操作符取地址后会获得这个变量的指针，然后可以对指针使用*操作，也就是指针取值，代码如下。

```
func main() {
	//指针取值
	a := 10
	b := &a // 取变量a的地址，将指针保存到b中
	fmt.Printf("type of b:%T\n", b)
	c := *b // 指针取值（根据指针去内存取值）
	fmt.Printf("type of c:%T\n", c)
	fmt.Printf("value of c:%v\n", c)
}
```

输出结果:

```
type of b:*int
type of c:int
value of c:10
```



### 4.指针特性

描述: 通过上面的`指标变量、类型、取值`的学习，我们了解到`取地址操作符&`和`取值操作符*`是一对互补操作符,其中`&`取出地址，`*`根据地址取出地址指向的值。

Tips : 变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：

- 1.对变量进行取地址（&）操作，可以获得这个变量的指针变量（`指针地址`）。
- 2.对指针变量进行取值（*）操作，可以获得指针变量指向的原变量的值。

例如:我们可以在局部函数中修改全局变量的值采用指针传值：

```
func modify1(x int) {
	x = 100
}

func modify2(x *int) {
	*x = 100
}

func main() {
	a := 10
	modify1(a)
	fmt.Println(a) // 10
	modify2(&a)
	fmt.Println(a) // 100
}
```



### 5.内存地址分配

描述: 在Go语言中对于引用类型的变量，我们在使用的时候不仅要声明它，还要`为它分配内存空间`，否则我们的值就没办法存储。而`对于值类型的声明不需要分配内存空间`，是因为它们在声明的时候已经默认分配好了内存空间。
Tips ：Go语言中new和make是内建的两个函数，他主要用来分配内存。

例如:执行下述例子中的代码会引发panic错误

```
func main() {
  // 声明
	var a *int
  // 定义
	*a = 100

	fmt.Println(*a)

	var b map[string]int
	b["沙河娜扎"] = 100
	fmt.Println(b)
}
```



**New 函数**
描述: new是Go语言的一置的函数它的函数签名如下：

```go
func new(Type) *Type
```

其中，

- `Type` 表示类型，new 函数只接受一个参数，这个参数是一个类型
- `*Type` 表示类型指针，new 函数返回一个指向该类型内存地址的指针。



Tips ：New 函数不太常用但由它可以得到一个类型的指针，并且该指针对应的值应该为该类型的零值。

```go
func main() {
  // 只是声明了一个指针变量a但是没有初始化
	a := new(int)
	b := new(bool)
	fmt.Printf("%T\n", a) // *int
	fmt.Printf("%T\n", b) // *bool
	fmt.Println(*a)       // 0
	fmt.Println(*b)       // false
}	
```

Tips : 指针作为引用类型需要初始化后才会拥有内存空间才可以给它赋值,所以需要按照下述方式使用内置的new函数对a进行初始化之后就可正常对其赋值了。

```go
func main() {
	var a *int
	a = new(int)
	*a = 10
	fmt.Println(*a)
}
```



**make 函数**
描述: make也是用于内存分配的，区别于new，它只用于slice、map以及chan的内存创建，而且它返回的类型就是这三个类型本身，而不是他们的指针类型，因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。

函数签名如下：

```
func make(t Type, size ...IntegerType) Type
```

Tips : Type 主要是 slice、map 以及channel类型，并且必须使用make进行初始化后，才能对它进行操作。

例如:

```go
func main() {
  // 只是声明变量b是一个map类型的变量
	var b map[string]int
  //使用make函数进行初始化操作之后
	b = make(map[string]int, 10)
  //才能对其进行键值对赋值：
	b["WeiyiGeek"] = 100
	fmt.Println(b)
}
```



**总结:new 函数与 make函数的区别**

- 二者都是用来做内存分配的。
- make只用于slice、map以及channel的初始化，返回的还是这三个引用类型本身；
- new用于类型的内存分配，并且内存对应的值为类型零值，返回的是指向类型的指针。



**示例演示:**

```go
// 转入int类型的参数
func normal(x int) {
	x = 65535
  fmt.Printf("Func Param &x ptr : %p \n", &x)
}

// 传入的参数为指针类型
func pointer(x *int) {
	*x = 65535
	fmt.Printf("Func Param x ptr : %p \n", x)
}

func demo1() {
	// 1.2获得变量a的内存地址
	a := 1024
	b := &a
	fmt.Printf("a : %d , a ptr: %p, b ptr : %v , *b = %d \n", a, &a, b, *b)
	fmt.Printf("b type: %T, &b ptr : %p \n", b, &b)
	fmt.Println()
	// 2.针对变量a的内存地址进行重赋值（此时会覆盖变量a的原值）
	*b = 2048
	fmt.Printf("Change -> a : %d , a ptr: %p, b ptr : %v , *b = %d \n", a, &a, b, *b)
	fmt.Printf("b type: %T, &b ptr : %p \n\n", b, &b)

	// 3.指针传值
	c := 4096
	normal(c)
	fmt.Println("After Normal Function c : ", c)
	pointer(&c)
	fmt.Printf("After Pointer Function c : %v, c ptr: %p \n\n", c, &c)

	// 4.new 内存地址申请
	var a4 *int
	//*a4 = 100 // 此行会报 _panic 错误，因为未分配内存空间
	fmt.Println("a4 ptr : ", a4) // 空指针 （<nil>）还没有内存地址

	d := new(int)                         // 申请一块内存空间 （内存地址）
	fmt.Printf("%T ，%p, %v \n", d, d, *d) // 其指针类型默认值为 0 与其类型相关联。
	*d = 8192                             // 对该内存地址赋值
	fmt.Printf("%T ，%p, %v \n\n", d, d, *d)

	// 5.make 内存地址申请
	var b5 map[string]string
	//b5["Name"] = "WeiyGeek" //此行会报 _panic 错误，因为未分配内存空间
	fmt.Printf("%T , %p , %v\n", b5, &b5, *&b5)

	b5 = make(map[string]string, 10) // 申请一块内存空间 （内存地址）
	b5["Name"] = "WeiyGeek"          // 此时便可对该Map类型进行赋值了
	b5["Address"] = "ChongQIng China"
	fmt.Printf("%T , %p , %v\n\n", b5, &b5, b5)
}
```

执行结果:

```go
a : 1024 , a ptr: 0xc00001a0d8, b ptr : 0xc00001a0d8 , *b = 1024 
b type: *int, &b ptr : 0xc00000e028 

Change -> a : 2048 , a ptr: 0xc00001a0d8, b ptr : 0xc00001a0d8 , *b = 2048 
b type: *int, &b ptr : 0xc00000e028 

Func Param &x ptr : 0xc00013a048 
After Normal Function c :  4096
Func Param x ptr : 0xc00013a040 
After Pointer Function c : 65535, c ptr: 0xc00013a040 

a4 ptr :  <nil>
*int ，0xc00001a130, 0 
*int ，0xc00001a130, 8192 

map[string]string , 0xc00000e038 , map[]
map[string]string , 0xc00000e038 , map[Address:ChongQIng China Name:WeiyGeek]
```



## 函数

### 建立

```go
func main(){
	sum:=sum(os.Args[1],os.Args[2])
	fmt.Println("Sum:",sum)
}
func sum(number1 string,number2 string)int{
	int1,_:=strconv.Atoi(number1)
	int2,_:=strconv.Atoi(number2)
	return int1+int2
}
```

返回多个值

```go
func main() {
	sum, mul := calc(os.Args[1], os.Args[2])
	fmt.Println("Sum:", sum)
	fmt.Println("Mul:", mul)
}
func calc(number1 string, number2 string) (sum int, mul int) {
	int1, _ := strconv.Atoi(number1)
	int2, _ := strconv.Atoi(number2)
	sum = int1 + int2
	mul = int1 * int2
	return
}
```

Go 的另一个有趣功能是，如果不需要函数的某个返回值，可以通过将返回值分配给 `_` 变量来放弃该函数。

修改函数的参数值（指针）

```go
package main

import "fmt"

func main() {
    firstName := "John"
    updateName(&firstName)
    fmt.Println(firstName)
}

func updateName(name *string) {
    *name = "David"
}
```

### 可变参数
描述: 可变参数是指函数的参数数量不固定。Go语言中的可变参数通过在参数名后加 ... 来标识(是否似曾相识，我们在数组那章节时使用过它，表示自动判断数组中元素个数进行初始化操作)。

示例1:

```go
func intSum2(x ...int) int {
  fmt.Println(x) //x是一个切片
  sum := 0
  for _, v := range x {
    sum = sum + v
   }
  return sum
}
```

调用上面的函数：

ret1 := intSum2()
ret2 := intSum2(10)
ret3 := intSum2(10, 20)
ret4 := intSum2(10, 20, 30)
fmt.Println(ret1, ret2, ret3, ret4) //0 10 30 60

注意：可变参数通常要作为函数的最后一个参数。



固定参数搭配可变参数使用时，可变参数要放在固定参数的后面，

示例2

```go
func intSum3(x int, y ...int) int {
  fmt.Println(x, y)
  sum := x
  for _, v := range y {
  	sum = sum + v
   }
  return sum
}
```

调用上述函数：

ret5 := intSum3(100)
ret6 := intSum3(100, 10)
ret7 := intSum3(100, 10, 20)
ret8 := intSum3(100, 10, 20, 30)
fmt.Println(ret5, ret6, ret7, ret8) //100 110 130 160

Tips : 本质上，函数的可变参数是通过切片来实现的



### 定义函数类型
描述: 我们可以使用type关键字来定义一个函数类型，具体格式如下：type calculation func(int, int) int

上面语句定义了一个calculation类型，它是一种函数类型，这种函数接收两个int类型的参数并且返回一个int类型的返回值。

简单来说，凡是满足这个条件的函数都是calculation类型的函数，例如下面的add和sub都是calculation类型的函数。

示例:

```go
type calculation func(int, int) int
func add(x, y int) int {
  return x + y
}
func sub(x, y int) int {
  return x - y
}
// add和sub都能赋值给calculation类型的变量。
var c calculation
c = add
fmt.Println(c(1,2)) // 函数类型变量传递
```


函数类型变量
描述: 我们可以声明函数类型的变量并且为该变量赋值：

```go
func main() {
  var c calculation               // 声明一个calculation类型的变量c
  c = add                         // 把add赋值给calculation类型的变量c
  fmt.Printf("type of c:%T\n", c) // type of c:main.calculation  (区别点)
  fmt.Println(c(1, 2))            // 像调用add一样调用c 1 + 2 = 3

  f := sub                        // 将函数sub赋值给变量f1
  fmt.Printf("type of f:%T\n", f) // type of f:func(int, int) int (区别点) 非函数类型的变量
  fmt.Println(f(30, 20))          // 像调用add一样调用f 30 - 20 = 10
}
```

### 高阶函数
描述: 高阶函数分为函数作为参数和函数作为返回值两部分。

函数作为参数
函数可以作为参数示例:

```go
func add(x, y int) int {
  return x + y
}
func calc(x, y int, op func(int, int) int) int {
  return op(x, y)
}
func main() {
  ret2 := calc(10, 20, add)
  fmt.Println(ret2) //30
}
```

函数作为返回值

函数也可以作为返回值示例(此种方式非常值得学习):

```go
func do(s string) (func(int, int) int, error) {
  switch s {
  case "+":
  return add, nil
  case "-":
  return sub, nil
  default:
  err := errors.New("无法识别的操作符")
  return nil, err
  }
} 
```



### 匿名函数
描述: 函数当然还可以作为返回值，但是在Go语言中函数内部不能再像之前那样定义函数了，只能定义匿名函数。

Q: 什么是匿名函数?

答: 匿名函数就是没有函数名的函数,在很多编程语言中都有这样的特性。
匿名函数多用于实现回调函数和闭包。

Tips : 匿名函数因为没有函数名，所以没办法像普通函数那样调用，所以匿名函数需要保存到某个变量或者作为立即执行函数:

```go
func main() {
  // 方式1.将匿名函数保存到变量
  add := func(x, y int) {
   fmt.Println(x + y)
  }
  add(10, 20) // 通过变量调用匿名函数

  //方式2.自执行函数：匿名函数定义完加()直接执行
  func(x, y int) {
    fmt.Println(x + y)
  }(10, 20)
} 
```



### 闭包

描述: 闭包指的是一个函数和与其相关的引用环境组合而成的实体。简单来说，**闭包=函数+外遍变量**的引用, 例如在第三方包里只能传递一个不带参数的函数，此时我们可以通过闭包的方式创建一个带参数处理的流程，并返回一个不带参数的函数。

Tips : 非常注意引用的外部外部变量在其生命周期内都是存在的（即下次调用还能使用该变量值）。



闭包基础示例1：

```go
func adder() func(int) int {
  var x int  // 在f的生命周期内，变量x也一直有效
  return func(y int) int {
    x += y
    return x
  }
}
func main() {
  var f = adder()
  fmt.Println(f(10)) //x=0,y=10 ->  x = 10
  fmt.Println(f(20)) //x=10,y=20 -> x = 30
  fmt.Println(f(30)) //x=30,y=30 -> x = 60

  f1 := adder()
  fmt.Println(f1(40)) //40
  fmt.Println(f1(50)) //90
}
```




闭包基础示例2:

```go
package main

import (
"fmt"
"math"
)

// 1.假设这是个第三方包
func f1(f func()) {
fmt.Printf("# This is f1 func , Param is f func() : %T \n", f)
f() // 调用传入的函数
}

// 2.自己实现的函数
func f2(x, y int) {
fmt.Printf("# This is f2 func , Param is x,y: %v %v\n", x, y)
fmt.Printf("x ^ y = %v \n", math.Pow(float64(x), float64(y)))
}
```

// 要求 f1(f2) 可以执行，此时由于f1 中的传递的函数参数并无参数，所以默认调用执行一定会报错。
// 此时我们需要一个中间商利用闭包和匿名函数来实现,返回一个不带参数的函数。

```go
func f3(f func(int, int), x, y int) func() {
tmp := func() {
f(x, y) // 此处实际为了执行f2函数
}
return tmp // 返回一个不带参数的函数，为返回给f1函数
}

func main() {
ret := f3(f2, 2, 10) // 此时函数并为执行只是将匿名函数进行返回。先执行 f3(fun,x,y int)
f1(ret)              // 当传入f1中时ret()函数便会进行执行。再执行 f1() ,最后执行 f2(x,y int)
}
```

执行结果:

This is f1 func , Param is f func() : func()

This is f2 func , Param is x,y: 2 10

x ^ y = 1024

Tips : 变量f是一个函数并且它引用了其外部作用域中的x变量，此时f就是一个闭包。并且在f的生命周期内，变量x也一直有效。



闭包进阶示例1：相比较于上面这种方式该种是将x变量放入函数参数之中，在进行函数调用时赋值。

```go
func adder2(x int) func(int) int {
  return func(y int) int {
  x += y
  return x
  }
}
func main() {
  var f = adder2(10) // `在f的生命周期内，变量x也一直有效。`
  fmt.Println(f(10)) //20
  fmt.Println(f(20)) //40
  fmt.Println(f(30)) //70

  f1 := adder2(20)
  fmt.Println(f1(40)) //60
  fmt.Println(f1(50)) //110
}
```


闭包进阶示例2：判断文件名称是否以指定的后缀结尾，是则返回原文件名称，否则返回文件名称+指定后缀的文件。

```go
func makeSuffixFunc(suffix string) func(string) string {
  return func(name string) string {
     // 判断name变量中的字符串是否已suffix结尾
  if !strings.HasSuffix(name, suffix) {
  return name + suffix
  }
  return name
  }
}

func main() {
  jpgFunc := makeSuffixFunc(".jpg")
  txtFunc := makeSuffixFunc(".txt")
  fmt.Println(jpgFunc("test")) //test.jpg
  fmt.Println(txtFunc("test")) //test.txt
}
```


闭包进阶示例3：该示例中函数同时返回add,sub两个函数.

```go
func calc(base int) (func(int) int, func(int) int) {
  add := func(i int) int {
  base += i
  return base
  }

  sub := func(i int) int {
  base -= i
  return base
  }
  return add, sub
}

func main() {
  f1, f2 := calc(10)
  fmt.Println(f1(1), f2(2)) //11 9
  fmt.Println(f1(3), f2(4)) //12 8
  fmt.Println(f1(5), f2(6)) //13 7
}
```

Important : 闭包其实并不复杂，只要牢记闭包=函数+引用环境 



## 控制流

### if

与c语言大体相同，注意代码规范

```go
func main() {
	x := 27
    if x%2 == 0 {//注意if与{在同一行
		fmt.Println(x, "is even")
    } else//注意}与else在同一行
	{
		 fmt.Println(x, "is ji")
	} 
}
如果 x是在if中定义的，那么if结束，它的作用域也结束
```



### switch

与c大体相同

```go
func main() {
	sec := time.Now().Unix()
	rand.Seed(sec)
	i := rand.Intn(2)
	switch i {
	case 0:
		fmt.Println("zero...")
	case 1:
		fmt.Println("one...")
	case 2:
		fmt.Println("two...")
	default:
		fmt.Println("no match...")
	}
	fmt.Println("ok")
}

```

```go
package main

import "fmt"

func location(city string) (string, string) {
    var region string
    var continent string
    switch city {
    case "Delhi", "Hyderabad", "Mumbai", "Chennai", "Kochi":
        region, continent = "India", "Asia"
    case "Lafayette", "Louisville", "Boulder":
        region, continent = "Colorado", "USA"
    case "Irvine", "Los Angeles", "San Diego":
        region, continent = "California", "USA"
    default:
        region, continent = "Unknown", "Unknown"
    }
    return region, continent
}
func main() {
    region, continent := location("Irvine")
    fmt.Printf("John works in %s, %s\n", region, continent)
}
```

```go
func main() {
	switch time.Now().Weekday().String() {
	case "Monday", "Tuesday", "Wednesday", "Thursday", "Friday":
		fmt.Println("It's time to learn some Go.")
	default:
		fmt.Println("It's weekend, time to rest!")
	}

	fmt.Println(time.Now().Weekday().String())
}//从 switch 语句调用函数时，无需更改表达式即可修改其逻辑，因为你始终会验证函数返回的内容。

```

```go
func main() {
	var email = regexp.MustCompile(`^[^@]+@[^@.]+\.[^@.]+`)
	var phone = regexp.MustCompile(`^[(]?[0-9][0-9][0-9][). \-]*[0-9][0-9][0-9][.\-]?[0-9][0-9][0-9][0-9]`)

	contact := "13632213436"

	switch {
	case email.MatchString(contact):
		fmt.Println(contact, "is an email")
	case phone.MatchString(contact):
		fmt.Println(contact, "is a phone number")
	default:
		fmt.Println(contact, "is not recognized")
	}
}//非常妙的函数使用

```

```go
package main

import (
    "fmt"
)

func main() {
    switch num := 15; {
    case num < 50:
        fmt.Printf("%d is less than 50\n", num)
        fallthrough
    case num > 100:
        fmt.Printf("%d is greater than 100\n", num)
        fallthrough
    case num < 200:
        fmt.Printf("%d is less than 200", num)
    }
}//fallthrough可以使逻辑会立即转到下一个 case 语句，而不会对该 case 进行验证。
```

### for

与c语言比，融合了while的功能

```go
func main() {
    sum := 0
    for i := 1; i <= 100; i++ {
        sum += i
    }
    fmt.Println("sum of 1..100 is", sum)
}
```

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    var num int64
    rand.Seed(time.Now().Unix())
    for num != 5 {
        num = rand.Int63n(15)
        fmt.Println(num)
    }
}
```

如果要用无限循环，则要用大括号，可以用break退出

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    var num int32
    sec := time.Now().Unix()
    rand.Seed(sec)

    for {
        fmt.Print("Writing inside the loop...")
        if num = rand.Int31n(10); num == 5 {
            fmt.Println("finish!")
            break
        }
        fmt.Println(num)
    }
}
```

continue与c语言一致

range 产生一对值；索引以及在该索引处的元素值

* 1. 数组、切片、字符串返回索引和值。

* 2. map返回键和值。

* 3. 通道（channel）只返回通道内的值。 

### defer

![WeiyiGeek.defer执行时机](C:\Users\你的姓名\Nutstore\1\我的坚果云\笔记\GO\6ea5df085af746954c716a7838370ceded6dab69.png@942w_354h_progressive.webp)

每次推迟 `fmt.Println("deferred", -i)` 时，都会存储 `i` 的值，并会将其运行任务添加到队列中。在 `main()` 函数输出完 `regular` 值后，所有推迟的调用都会运行。

```go
func main() {
	for i := 1; i <= 4; i++ {
		defer fmt.Println("defered", -i)
		fmt.Println("regular", i)
	}
}
```

```go
package main
import "fmt"
// 函数返回值无命名
func f1() int {
	x := 5 // 局部变量
	defer func() {
		x++
	}()
	return x // 1.返回值 x = 5, 2.defer 语句执行后修改的是 x = 6，3.RET指令最后返回的值是 5 (由于无返回值命令则就是return已赋予的值5)
}

// 函数返回值命名 y 进行返回
func f2() (x int) {
	defer func() {
		x++
	}()
	return 5 // 1.返回值 x = 5, 2.defer 语句执行后修改的是 x = 6，3.RET指令最后返回的x值是 6 (由于存在返回值命名x则就是return x 值6)
}

// 函数返回值命名 y 进行返回
func f3() (y int) {
	x := 5 // 局部变量
	defer func() {
		x++ // 修改 x 变量的值 x + 1
	}()
	return x // 1.返回值 x = y = 5, 2.defer 语句执行后修改的是 x ，3.RET指令最后返回的y值还是 5
}

// 匿名函数无返回值
func f4() (x int) {
	defer func(x int) {
		x++ // 改变得是函数中局部变量x，非外部x变量。
	}(x)
	return 5 // 1.返回值 x = 5, 2.defer 语句执行后 x 副本 = 6 , 3.RET指令最后返回的值还是 5
}

// 匿名函数中返回值
func f5() (x int) {
	defer func(x int) int {
		x++ // 改变得是函数中局部变量x，非外部x变量。
		return x
	}(x)
	return 5 // 1.返回值 x = 5, 2.defer 语句执行后 x 副本 = 6 , 3.RET指令最后返回的值还是 5
}

// 传入一个指针到匿名函数中(方式1)
func f6() (x int) {
	defer func(x *int) {
		*x++
	}(&x)
	return 5 // 1.返回值 x = 5, 2.由于defer语句，传入x指针地址到匿名函数中 x = 6, 3.RET指令最后返回的值 6
}

// 传入一个指针到匿名函数中(方式2)
func f7() (x int) {
	defer func(x *int) int {
		(*x)++
		return *x
	}(&x)
	return 5 // 1.返回值x = 5, 2.由于defer语句，传入x指针地址到匿名函数中 x = 6, 3.RET指令最后返回值 6
}

func main() {
	fmt.Println("f1() = ", f1())
	fmt.Println("f2() = ", f2())
	fmt.Println("f3() = ", f3())
	fmt.Println("f4() = ", f4())
	fmt.Println("f5() = ", f5())
	fmt.Println("f6() = ", f6())
	fmt.Println("f7() = ", f7())
}
// 执行结果:
f1() =  5
f2() =  6
f3() =  5
f4() =  5
f5() =  5
f6() =  6
f7() =  6 
```

```go
func calc(index string, a, b int) int {
 ret := a + b
 fmt.Println(index, a, b, ret)
 return ret
}

func main() {
 x := 1
 y := 2
 defer calc("AA", x, calc("A", x, y))
 // calc("A", x, y) =>calc("A", 1, 2) = 3  {"A" , 1, 2, 3}
 // defer calc("AA", 1, 3) = 4 {"AA", 1, 3, 4}
 x = 10
 defer calc("BB", x, calc("B", x, y))
 // calc("B", x, y) = calc("B", 10, 2) = 12  {"B" , 10, 2, 12}
 // defer calc("BB", 10, 12) = 22 {"BB",10,12,22}
 y = 20
}
// 执行结果:
{"A" , 1, 2, 3}
{"B" , 10, 2, 12}
{"BB", 10, 12, 22}
{"AA", 1, 3, 4}
```

defer注册要延迟执行的函数时该函数所有的参数都需要确定其值 Tips : 当遇到defer语句时其中的函数中调用的变量值是外部变量时，是离该defer语句最近的外部变量其赋予的值(存在于一个变量多次赋值的场景)。

### panic

panic（运行时恐慌）是一种只会在程序运行时才回抛出来的异常。在panic被抛出之后，如果没有在程序里添加任何保护措施的话，程序就会在打印出panic的详情，终止运行。(人工设置报错条件和报错信息)

```go
package main

import "fmt"

func highlow(high int, low int) {
    if high < low {
        fmt.Println("Panic!")
        panic("highlow() low greater than high")
    }
    defer fmt.Println("Deferred: highlow(", high, ",", low, ")")
    fmt.Println("Call: highlow(", high, ",", low, ")")

    highlow(high, low + 1)
}

func main() {
    highlow(2, 0)
    fmt.Println("Program finished successfully!")
}
```

panic的函数并不会立刻返回，而是先defer，再返回

### recover

一旦panic，逻辑就会走到defer那，那我们就在defer那等着，调用recover函数将会捕获到当前的panic（如果有的话），被捕获到的panic就不会向上传递了，于是，世界恢复了和平。

```go
func main() {
    defer func() {
	handler := recover()
        if handler != nil {
            fmt.Println("main(): recover", handler)
        }
    }()//因为是在函数内定义表示调用这个函数

    highlow(2, 0)
    fmt.Println("Program finished successfully!")
}
```

第二次走到 fmt.[Scanf](https://so.csdn.net/so/search?q=Scanf&spm=1001.2101.3001.7020) 时，程序直接跳过了输入

**需要在scanf里面加入\n 换行符才能执行** 



### ++/--

自增语句 i++ 给 i 加1；这和 i += 1 以及 i = i + 1 都是等价的。对应的还有 i-- 给 i 减 1。它们是语句，而不像C系的其它语言那样是表达式。所以 j = i++ 非法，而且++和--都只 能放在变量名后面，因此 --i 也非法。

## 数组

与python类似

让我们修改在上一部分中使用的程序，以使用省略号。 代码应如下例所示：



```go
package main

import "fmt"

func main() {
    cities := [...]string{"New York", "Paris", "Berlin", "Madrid"}
    fmt.Println("Cities:", cities)
}
```

运行上述代码，你应看到如下例所示的输出：

```output
Cities: [New York Paris Berlin Madrid]
```
```go
func main() {
    numbers := [...]int{99: -1}
    fmt.Println("First Position:", numbers[0])
    fmt.Println("Last Position:", numbers[99])
    fmt.Println("Length:", len(numbers))
}
```

运行此代码，你将获得以下输出：

```
First Position: 0
Last Position: -1
Length: 100
```

请注意数组的长度是 100，因为你为第 99 个位置指定了一个值。 第一个位置打印出默认值（零）。

让我们看一下追加进程在代码中的显示方式：

```go
package main

import "fmt"

func main() {
    var numbers []int
    for i := 0; i < 10; i++ {
        numbers = append(numbers, i)
        fmt.Printf("%d\tcap=%d\t%v\n", i, cap(numbers), numbers)
    }//cap()函数返回的是数组切片分配的空间大小
}
```

运行上述代码时，你应会看到以下输出：

```output
0       cap=1   [0]
1       cap=2   [0 1]
2       cap=4   [0 1 2]
3       cap=4   [0 1 2 3]
4       cap=8   [0 1 2 3 4]
5       cap=8   [0 1 2 3 4 5]
6       cap=8   [0 1 2 3 4 5 6]
7       cap=8   [0 1 2 3 4 5 6 7]
8       cap=16  [0 1 2 3 4 5 6 7 8]
9       cap=16  [0 1 2 3 4 5 6 7 8 9]
```

此输出很有意思。 特别是对于调用 `cap()` 函数所返回的内容。 一切看起来都很正常，直到第 3 次迭代，此时容量变为 4，切片中只有 3 个元素。 在第 5 次迭代中，容量又变为 8，第 9 次迭代时变为 16。

 当切片容量不足以容纳更多元素时，Go 的容量将翻倍。 它将新建一个具有新容量的基础数组。 无需执行任何操作即可使容量增加。 **Go 会自动扩充容量。 需要谨慎操作**。 有时，一个切片具有的容量可能比它需要的多得多，这样你将会浪费内存。



 Go 没有内置函数用于从切片中删除元素。 可使用上述切片运算符 `s[i:p]` 来新建一个仅包含所需元素的切片。（具体操作有点蠢）

```go
package main

import "fmt"

func main() {
    letters := []string{"A", "B", "C", "D", "E"}
    remove := 2

	if remove < len(letters) {

		fmt.Println("Before", letters, "Remove ", letters[remove])

		letters = append(letters[:remove], letters[remove+1:]...)

		fmt.Println("After", letters)
	}

}
```

## 映射

在将项添加到映射时出现问题，请确保使用 `make` 函数（如我们在上述代码片段中所示）创建一个空映射（而不是 `nil` 映射）

```go
func main() {
    studentsAge := map[string]int{
        "john": 32,
        "bob":  31,
    }
    studentsAge := make(map[string]int)
    //var studentsAge map[string]int 寄
    studentsAge["john"] = 32
    studentsAge["bob"] = 31
    fmt.Println(studentsAge)
}
```

在映射中使用下标表示法时，即使映射中没有键值对，你也总会获得响应。 当你访问不存在的项时，Go 不会执行 panic。 此时，你会获得默认值0。

**在 Go 中，映射的下标表示法可生成两个值。 第一个是项的值。 第二个是指示键是否存在的布尔型标志。**

```go
age, exist := studentsAge["christy"]
    if exist {
        fmt.Println("Christy's age is", age)
    } else {
        fmt.Println("Christy's age couldn't be found")
    }
```

可以用delete()删除

```go
scene["route"] = 66
scene["brazil"] = 4
scene["china"] = 960
delete(scene, "brazil")
```
遍历可以用`range`
```go
package main

import (
    "fmt"
)

func main() {
    studentsAge := make(map[string]int)
    studentsAge["john"] = 32
    studentsAge["bob"] = 31
    for name, age := range studentsAge// for _, age := range studentsAge// for name := range studentsAge{
        fmt.Printf("%s\t%d\n", name, age)
    }
}
```



`range` 会首先生成项的键，然后再生成该项的值。

有哪项不想要可以用_代替

## 结构

```go
type Employee struct {
	ID        int
	FirstName string
	LastName  string
	Address   string
}

func main() {
	employee := Employee{LastName: "Doe", FirstName: "John"}
	fmt.Println(employee)
	employeeCopy := &employee
	employeeCopy.FirstName = "David"
	fmt.Println(employee)
}

```

```go
package main

import "fmt"

type Person struct {
    ID        int
    FirstName string
    LastName  string
    Address   string
}

type Employee struct {
    Person
    ManagerID int
}

type Contractor struct {
    Person
    CompanyID int
}

func main() {
    employee := Employee{
        Person: Person{
            FirstName: "John",
        },
    }
    employee.LastName = "Doe"
    fmt.Println(employee.FirstName)
}//请注意如何在无需指定 Person 字段的情况下访问 Employee 结构中的 FirstName 字段，因为它会自动嵌入其所有字段。 但在你初始化结构时，必须明确要给哪个字段分配值。
```





```go
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    ID        int
    FirstName string `json:"name"`
    LastName  string
    Address   string `json:"address,omitempty"`
}

type Employee struct {
    Person
    ManagerID int
}

type Contractor struct {
    Person
    CompanyID int
}

func main() {
    employees := []Employee{
        Employee{
            Person: Person{
                LastName: "Doe", FirstName: "John",
            },
        },
        Employee{
            Person: Person{
                LastName: "Campbell", FirstName: "David",
            },
        },
    }

    data, _ := json.Marshal(employees)
    fmt.Printf("%s\n", data)

    var decoded []Employee
    json.Unmarshal(data, &decoded)
    fmt.Printf("%v", decoded)
}//要将结构编码为 JSON，请使用 json.Marshal 函数。 若要将 JSON 字符串解码为数据结构，请使用 json.Unmarshal 函数。 下例将所有内容组合在一起，将员工数组编码为 JSON，并将输出解码为新的变量：
```
## 方法
```go
package main

import "fmt"

type triangle struct {
    size int
}

type square struct {
    size int
}

func (t triangle) perimeter() int {
    return t.size * 3
}

func (s square) perimeter() int {
    return s.size * 4
}

func (t *triangle) doubleSize() {
    t.size *= 2
}//可以通过指针修改t中的值

func main() {
    t := triangle{3}
    s := square{4}
    fmt.Println("Perimeter (triangle):", t.perimeter())
    fmt.Println("Perimeter (square):", s.perimeter())
}
```

### 声明其他类型的方法

方法的一个关键方面在于，需要为任何类型定义方法，而不只是针对自定义类型（如结构）进行定义。 但是，你不能通过属于其他包的类型来定义结构。 因此，不能在基本类型（如 `string`）上创建方法。

尽管如此，你仍然可以利用一点技巧，基于基本类型创建自定义类型，然后将其用作基本类型。 例如，假设你要创建一个方法，以将字符串从小写字母转换为大写字母。 你可以按如下所示写入方法：

Go复制

```go
package main

import (
    "fmt"
    "strings"
)

type upperstring string

func (s upperstring) Upper() string {
    return strings.ToUpper(string(s))
}

func main() {
    s := upperstring("Learning Go!")
    fmt.Println(s)
    fmt.Println(s.Upper())
}
```

### 嵌入方法

例如，假设你想要创建一个带有逻辑的新三角形结构，以加入颜色。 此外，你还希望继续使用之前声明的三角形结构。 因此，彩色三角形结构将如下所示：

Go复制

```go
type coloredTriangle struct {
    triangle
    color string
}
```

然后，你可以初始化 `coloredTriangle` 结构，并从 `triangle` 结构调用 `perimeter()` 方法（甚至访问其字段），具体如下所示：

```go
func main() {
    t := coloredTriangle{triangle{3}, "blue"}
    fmt.Println("Size:", t.size)
    fmt.Println("Perimeter", t.perimeter())
}
```

继续操作并在程序中加入上述更改，以了解嵌入的工作方式。 当使用类似于上一个方法的 `main()` 方法运行程序时，你将看到以下输出：

```output
Size: 3
Perimeter 9
```

如果你熟悉 Java 或 C++ 等 OOP 语言，则可能会认为 `triangle` 结构看起来像基类，而 `coloredTriangle` 是一个子类（如继承），**但事实并不是如此。 实际上，Go 编译器会通过创建如下的包装器方法来推广** `perimeter()` 方法：

```go
func (t coloredTriangle) perimeter() int {
    return t.triangle.perimeter()
}
```

请注意，接收方是 `coloredTriangle`，它从三角形字段调用 `perimeter()` 方法。 好的一点在于，你不必再创建之前的方法。 你可以选择创建，但 Go 已在内部为你完成了此工作。

### 重载方法

```go
func (t coloredTriangle) perimeter() int {
    return t.size * 3 * 2
}//放在上面的那个例子t.perimeter()会被替换为这个
//想要调用Triangle里的要这样写t.triangle.perimeter()
```

### 方法中的封装

“封装”表示对象的发送方（客户端）无法访问某个方法。 通常，在其他编程语言中，你会将 `private` 或 `public` 关键字放在方法名称之前。 **在 Go 中，只需使用大写标识符，即可公开方法，使用非大写的标识符将方法设为私有方法。**

Go 中的封装仅在程序包之间有效。 换句话说，你只能隐藏来自其他程序包的实现详细信息，而不能隐藏程序包本身。

与C++类似如果一个方法中的变量不是大写，那么只能够通过方法内函数来赋值

## 接口

```go
package main

import (
	"fmt"
	"math"
	// "github.com/myuser/geometry"
)

type Shape interface {
	Perimeter() float64
	Area() float64
}
type Square struct {
	size float64
}

func (s Square) Area() float64 {
	return s.size * s.size
}

func (s Square) Perimeter() float64 {
	return s.size * 4
}

type Circle struct {
	radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * c.radius * c.radius
}

func (c Circle) Perimeter() float64 {
	return 2 * math.Pi * c.radius
}
func printInformation(s Shape) {
	fmt.Printf("%T\n", s)
	fmt.Println("Area: ", s.Area())
	fmt.Println("Perimeter:", s.Perimeter())
	fmt.Println()
}
func main() {
	var s Shape = Square{3}
	printInformation(s)

	c := Circle{6}
	printInformation(c)
}
```



Go 中的接口是满足隐式实现的。 Go 并不提供用于实现接口的关键字

Go 中的接口是一种抽象类型，只包括具体类型必须拥有或实现的方法。

```go
type Shape interface {
    Perimeter() float64
    Area() float64
}
```

`Shape` 接口表示你想要考虑 `Shape` 的任何类型都需要同时具有 `Perimeter()` 和 `Area()` 方法。 例如，在创建 `Square` 结构时，它必须实现两种方法，而不是仅实现一种。 另外，请注意接口不包含这些方法的实现细节（例如，用于计算某个形状的周长和面积）。 **接口仅表示一种协定。 三角形、圆圈和正方形等形状有不同的计算面积和周长方式。**

```go
func printInformation(s Shape) {
    fmt.Printf("%T\n", s)
    fmt.Println("Area: ", s.Area())
    fmt.Println("Perimeter:", s.Perimeter())
    fmt.Println()
}
```



使用接口的优点在于，对于 `Shape`的每个新类型或实现，`printInformation` 函数都不需要更改。 正如之前所述，当你使用接口时，代码会变得更灵活、更容易扩展。

### 实现字符串接口

扩展现有功能的一个简单示例是使用 `Stringer`，它是具有 `String()` 方法的接口，具体如下所示：

Go复制

```go
type Stringer interface {
    String() string
}
```

`fmt.Printf` 函数使用此接口来输出值，这意味着你可以编写自定义 `String()` 方法来打印自定义字符串

```go
package main

import "fmt"

type Person struct {
    Name, Country string
}

func (p Person) String() string {
    return fmt.Sprintf("%v is from %v", p.Name, p.Country)
}
func main() {
    rs := Person{"John Doe", "USA"}
    ab := Person{"Mark Collins", "United Kingdom"}
    fmt.Printf("%s\n%s\n", rs, ab)
}
```

### 案例

#### 扩展现有实现

`io.Copy(os.Stdout, resp.Body)` 调用是指将通过对 GitHub API 的调用获取的内容打印到终端。 假设你想要写入自己的实现以缩短你在终端中看到的内容。 在查看 函数的源 时，你将看到以下内容：

Go复制

```go
func Copy(dst Writer, src Reader) (written int64, err error)
```

如果你深入查看第一个参数 `dst Writer` 的详细信息，你会注意到 `Writer` 是 `dst Writer`：

```go
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

你可以继续浏览 `io` 程序包的源代码，直到找到 `io`的位置。 我们暂时不做任何处理。

由于 `Writer` 是接口，并且是 `Copy` 函数需要的对象，你可以编写 `Write` 方法的自定义实现。 因此，你可以**自定义打印到终端的内容**。

实现接口所需的**第一项操作是创建自定义类型。** 在这种情况下，你可以创建一个空结构，因为你只需按如下所示编写自定义 `Write` 方法即可：

```go
type customWriter struct{}
```

现在，你已准备就绪，可开始编写自定义 `Write` 函数。 此时，你还需要编写一个结构，以便将 JSON 格式的 API 响应解析为 Golang 对象。 你可以使用“JSON 转 Go”站点从 JSON 有效负载创建结构。 因此，`Write` 方法可能如下所示：

```go
type GitHubResponse []struct {
    FullName string `json:"full_name"`
}

func (w customWriter) Write(p []byte) (n int, err error) {
    var resp GitHubResponse
    json.Unmarshal(p, &resp)
    for _, r := range resp {
        fmt.Println(r.FullName)
    }
    return len(p), nil
}
```

最后，你必须修改 `main()` 函数以使用你的自定义对象，具体如下所示：

```go
package main

import (
    "encoding/json"
    "fmt"
    "io"
    "net/http"
    "os"
)

type GitHubResponse []struct {
    FullName string `json:"full_name"`
}

type customWriter struct{}

func (w customWriter) Write(p []byte) (n int, err error) {
    var resp GitHubResponse
    json.Unmarshal(p, &resp)
    for _, r := range resp {
        fmt.Println(r.FullName)
    }
    return len(p), nil
}

func main() {
    resp, err := http.Get("https://api.github.com/users/microsoft/repos?page=15&per_page=5")
    if err != nil {
        fmt.Println("Error:", err)
        os.Exit(1)
    }

    writer := customWriter{}
    io.Copy(writer, resp.Body)//Copy会优先在main里找writer里的方法有没有Write,相当于原先的被优先级更高的替
}
```



## 并发

### Channel 语法

如果希望 channel 仅发送数据，则必须在 channel 之后使用 `<-` 运算符。 如果希望 channel 接收数据，则必须在 channel 之前使用 `<-` 运算符

```go
ch <- x // sends (or write) x through channel ch
x = <-ch // x receives (or reads) data sent to the channel ch
<-ch // receives data, but the result is discarded
```

可在 channel 中执行的另一项操作是关闭 channel。 若要关闭 channel，需使用内置的 `close()` 函数，如下所示：

```go
close(ch)
```
#### 无缓冲
```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

func checkAPI(api string, ch chan string) {
	_, err := http.Get(api)
	if err != nil {
		ch <- fmt.Sprintf("ERROR: %s is down!\n", api)
		return
	}

	ch <- fmt.Sprintf("SUCCESS: %s is up and running!\n", api)
}

func main() {
	start := time.Now()
	ch := make(chan string)//首先先要每次声明一个 channel 或希望在函数中指定一个 channel 作为参数时，都需要使用 chan <type>，如 chan string
	apis := []string{
		"https://management.azure.com",
		"https://dev.azure.com",
		"https://api.github.com",
		"https://outlook.office.com/",
		"https://api.somewhereintheinternet.com/",
		"https://graph.microsoft.com",
	}

	for _, api := range apis {
		go checkAPI(api, ch)//go一次直接又go，多线程开搞
	}
	for i := 0; i < len(apis); i++ {
		fmt.Print(<-ch)//接收了一次就过，所以最快的最先出来
	}
	// time.Sleep(3 * time.Second)
	elapsed := time.Since(start)
	fmt.Printf("Done! It took %v seconds!\n", elapsed.Seconds())
}

```

无缓冲 channel 在同步发送和接收操作。 即使使用并发，通信也是同步的。

#### 有缓冲

```go
package main

import (
	"fmt"
)

func send(ch chan string, message string) {
	ch <- message
}//把message送进栈

func main() {
	size := 2
	ch := make(chan string, size)//设置ch的大小为2个字符串
	send(ch, "one")
	send(ch, "two")
	go send(ch, "three")//相当于把这条放在另一个生产线，由于ch满了所以就阻塞，等下面ch中元素出去再进
	go send(ch, "four")
	fmt.Println("All data sent to the channel ...")

	for i := 0; i < 4; i++ {
		fmt.Println(<-ch)//ch中的元素出
	}

	fmt.Println("Done!")
}
```

#### select

```go
package main

import (
	"fmt"
	"time"
)

func process(ch chan string) {
	time.Sleep(3 * time.Second)
	ch <- "Done processing!"
}

func replicate(ch chan string) {
	time.Sleep(1 * time.Second)
	ch <- "Done replicating!"
}

func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)
	go process(ch1)
	go replicate(ch2)

	for i := 0; i < 2; i++ {
		select {
		case process := <-ch1:
			fmt.Println(process)
		case replicate := <-ch2:
            fmt.Println(replicate)
		}//由于存在睡眠所以replicate会先有数据接受，同时结束掉第一次select,进入第二次
	}
}
```

在运行 select 时，会遍历所有（如果有机会的话）的 case 表达式，只要有一个信道有接收到数据，那么 select 就结束

如果在遍历完所有的 case 后，若没有命中（`命中`：也许这样描述不太准确，我本意是想说可以执行信道的操作语句）任何一个 case 表达式，就会进入 default 里的代码分支。

但如果你没有写 default 分支，select 就会阻塞，直到有某个 case 可以命中，而如果一直没有命中，select 就会抛出 `deadlock` 的错误.

- select 不同与switch是随机进行的

- 读取/写入都可以

当 case 里的信道始终没有接收到数据时，而且也没有 default 语句时，select 整体就会阻塞，但是有时我们并不希望 select 一直阻塞下去，这时候就可以手动设置一个超时时间。

  

  ```go
  package main
  
  import (
      "fmt"
      "time"
  )
  
  func makeTimeout(ch chan bool, t int) {
      time.Sleep(time.Second * time.Duration(t))
      ch <- true
  }
  
  func main() {
      c1 := make(chan string, 1)
      c2 := make(chan string, 1)
      timeout := make(chan bool, 1)
  
      go makeTimeout(timeout, 2)
  
      select {
      case msg1 := <-c1:
          fmt.Println("c1 received: ", msg1)
      case msg2 := <-c2:
          fmt.Println("c2 received: ", msg2)
      case <-timeout:
          fmt.Println("Timeout, exit.")
      }
  }
  ```

 ## **常用函数**

 ### cap() 返回数组切片分配的空间大小

```go
package main

import "fmt"

func main() {
	
	test1 := make([]int,1024)
	test1 = append(test1,1)
	fmt.Printf("test1 len is %d\n",len(test1))
	fmt.Printf("test1 cap is %d\n", cap(test1))
	test2 := make([]int,4)
	test2 = append(test2,1)
	fmt.Printf("test2 len is %d\n",len(test2))
	fmt.Printf("test2 cap is %d", cap(test2))

}
test1 len is 1025 //1024+1
test1 cap is 1280 //1024*1.25
test2 len is 5	//4+1
test2 cap is 8	//4*2
Process exiting with code: 0
```

可以明显的看到len是当前的数组张度，而cap是实际占用



### make() 创建内置的数据结构

```go
slice := make([]int, 0, 100)
hash := make(map[int]bool, 10)
ch := make(chan int, 5)
```

1. `slice` 是一个包含 `data`、`cap` 和 `len` 的结构体 [`reflect.SliceHeader`](https://draveness.me/golang/tree/reflect.SliceHeader)；
2. `hash` 是一个指向 [`runtime.hmap`](https://draveness.me/golang/tree/runtime.hmap) 结构体的指针；
3. `ch` 是一个指向 [`runtime.hchan`](https://draveness.me/golang/tree/runtime.hchan) 结构体的指针；

### copy()一个切片内容复制到另一个切片中

- copy(目标切片, 源切片)

## 错误处理

error接口定义:

```go
type error interface {
  Error() string
}
// 方式1.在errors包中的New方法（Go 1.13 版本）。
package errors
// go提供了errorString结构体，其则实现了error接口
type errorString struct {
  text string
}
func (e *errorString) Error() string {
  return e.text
}

// 在errors包中，还提供了New函数，来实例化errorString，如下：
func New(text string) error {
  return &errorString{text}
}

// 方式2.另一个可以生成error类型值的方法是调用fmt包中的Errorf函数(Go 1.13 版本以后)
package fmt
import "errors"
func Errorf(format string, args ...interface{}) error{
	return errors.New(Sprintf(format,args...))
}
```
采用 errors 包中装饰一个错误;

errors.Unwrap(err error)	//通过 errors.Unwrap 函数得到被嵌套的 error。	

```go
func main() {
    e := errors.New("原始错误e")
    w := fmt.Errorf("Wrap了一个错误%w", e)
    fmt.Println(errors.Unwrap(w))
}
```

以上这个例子，通过`errors.Unwrap(w)`后，返回的其实是个`e`，也就是被嵌套的那个error。
这里需要注意的是，嵌套可以有很多层，我们调用一次`errors.Unwrap`函数只能返回最外面的一层`error`，如果想获取更里面的，需要调用多次`errors.Unwrap`函数。最终如果一个`error`不是warpping error，那么返回的是`nil`。

------

errors.Is(err, target error)	//用来判断两个 error 是否是同一个	

1. 如果`err`和`target`是同一个，那么返回`true`

2. 如果`err` 是一个wrap error,`target`也包含在这个嵌套error链中的话，那么也返回`true`。

   ------

   

errors.As(err error, target interface{})	//error 断言	


```go
package main

import (
    "fmt"
    "os"
)

type Employee struct {
    ID        int
    FirstName string
    LastName  string
    Address   string
}

func main() {
    employee, err := getInformation(1001)
    if err != nil {
        // Something is wrong. Do something.
    } else {
        fmt.Print(employee)
    }
}

func getInformation(id int) (*Employee, error) {
    employee, err := apiCallEmployee(1000)
    return employee, err
}

func apiCallEmployee(id int) (*Employee, error) {
    employee := Employee{LastName: "Doe", FirstName: "John"}
    return &employee, nil
```

### 错误处理策略

一个常见策略是继续使用该模式在子例程中传播错误。 例如，子例程（如上一示例中的 `getInformation`）可能会将错误返回给调用方

```go
func getInformation(id int) (*Employee, error) {
    employee, err := apiCallEmployee(1000)
    if err != nil {
        return nil, err // Simply return the error to the caller.
    }
    return employee, nil
}
```

你可能还需要在传播错误之前添加更多信息。 为此，可以使用 `fmt.Errorf()` 函数，该函数与我们之前看到的函数类似，但它返回一个错误。 例如，你可以向错误添加更多上下文，但仍返回原始错误

```go
func getInformation(id int) (*Employee, error) {
    employee, err := apiCallEmployee(1000)
    if err != nil {
        return nil, fmt.Errorf("Got an error when getting the employee information: %v", err)
    }
    return employee, nil
}//可以使用fmt.Println(err.Errorf)来打印错误
```

另一种策略是在错误为暂时性错误时运行重试逻辑。 例如，可以使用重试策略调用函数三次并等待两秒钟

```go
func getInformation(id int) (*Employee, error) {
    for tries := 0; tries < 3; tries++ {
        employee, err := apiCallEmployee(1000)
        if err == nil {
            return employee, nil
        }

        fmt.Println("Server is not responding, retrying ...")
        time.Sleep(time.Second * 2)
    }

    return nil, fmt.Errorf("server has failed to respond to get the employee information")
}
```



### 创建可重用的错误

有时错误消息数会增加，你需要维持秩序。 或者，你可能需要为要重用的常见错误消息创建一个库。 在 Go 中，你可以使用 `errors.New()` 函数创建错误并在若干部分中重复使用这些错误，如下所示：

```go
var ErrNotFound = errors.New("Employee not found!")

func getInformation(id int) (*Employee, error) {
    if id != 1001 {
        return nil, ErrNotFound
    }

    employee := Employee{LastName: "Doe", FirstName: "John"}
    return &employee, nil
}
```

`getInformation` 函数的代码外观更优美，而且如果需要更改错误消息，只需在一个位置更改即可。 另请注意，惯例是为错误变量添加 `Err` 前缀。

最后，如果你具有错误变量，则在处理调用方函数中的错误时可以更具体。 `errors.Is()` 函数允许你比较获得的错误的类型，如下所示：

```go
employee, err := getInformation(1000)
if errors.Is(err, ErrNotFound) {
    fmt.Printf("NOT FOUND: %v\n", err)
} else {
    fmt.Print(employee)
}
```

### 自定义错误类型

```go
package main

import (
    "fmt"
)

// 定义一个 DivideError 结构 (值得学习)
type DivideError struct {
  dividee int
  divider int
}
// 实现 `error` 接口 (值得学习)
func (de *DivideError) Error() string {
  strFormat := `
  Cannot proceed, the divider is zero.
  dividee: %d
  divider: 0
`
  return fmt.Sprintf(strFormat, de.dividee)
}

// 定义 `int` 类型除法运算的函数
func Divide(varDividee int, varDivider int) (result int, errorMsg string) {
  if varDivider == 0 {
    dData := DivideError{
            dividee: varDividee,
            divider: varDivider,
    }
    errorMsg = dData.Error()
    return
  } else {
    return varDividee / varDivider, ""
  }
}

func main() {
  // 正常情况
  if result, errorMsg := Divide(100, 10); errorMsg == "" {
    fmt.Println("100/10 = ", result)
  }
  // 当除数为零的时候会返回错误信息
  if _, errorMsg := Divide(100, 0); errorMsg != "" {
    fmt.Println("errorMsg is: ", errorMsg)
  }
}
```



### 用于错误处理的推荐做法

- 始终检查是否存在错误，即使预期不存在。 然后正确处理它们，以免向最终用户公开不必要的信息。
- 在错误消息中包含一个前缀，以便了解错误的来源。 例如，可以包含包和函数的名称。
- 创建尽可能多的可重用错误变量。
- 了解使用返回错误和 panic 之间的差异。 不能执行其他操作时再使用 panic。 例如，如果某个依赖项未准备就绪，则程序运行无意义（除非你想要运行默认行为）。
- 在记录错误时记录尽可能多的详细信息（我们将在下一部分介绍记录方法），并打印出最终用户能够理解的错误。



## 记录

### log包

#### log.Print 

下面是使用日志的最简单方法：

```go
import (
    "log"
)

func main() {
    log.Print("Hey, I'm a log!")
}
```

运行前面的代码时，将看到以下输出：

输出复制

```go
2020/12/19 13:39:17 Hey, I'm a log!
```
#### log.Fatal()
函数记录错误并结束程序你可以使用 `log.Fatal()` 函数记录错误并结束程序，就像使用 `os.Exit(1)` 一样。 使用以下代码片段试一试：

```go
package main

import (
    "fmt"
    "log"
)

func main() {
    log.Fatal("Hey, I'm an error log!")
    fmt.Print("Can you see me?")
}
```

运行前面的代码时，将看到以下输出：

输出复制

```go
2020/12/19 13:53:19  Hey, I'm an error log!
exit status 1
```
#### log.panic()
与fatal类似，会触发pinic
#### log.Setprefix()
向程序的日志消息添加前缀
```go
package main

import (
    "log"
)

func main() {
    log.SetPrefix("main(): ")
    log.Print("Hey, I'm a log!")
    log.Fatal("Hey, I'm an error log!")
}
```
运行前面的代码时，将看到以下输出：
```
main(): 2021/01/05 13:59:58 Hey, I'm a log!
main(): 2021/01/05 13:59:58 Hey, I'm an error log!
exit status 1
```

#### log.SetOutput()

Go复制

```go
package main

import (
    "log"
    "os"
)

func main() {
    file, err := os.OpenFile("info.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
    if err != nil {
        log.Fatal(err)
    }

    defer file.Close()

    log.SetOutput(file)
    log.Print("Hey, I'm a log!")
}
```

### zerolog 框架

**专注于记录 JSON 格式的日志**

[Go 每日一库之 zerolog - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1693827)



## 获取URL

~~~ go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
)

func main() {
	for _, url := range os.Args[1:] {
		resp, err := http.Get(url)//创建HTTP请求
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
			os.Exit(1)
		}
		b, err := ioutil.ReadAll(resp.Body)//response中读取到全部内容,将其结果保存在变量b中,resp的Body字段包括一个可读的服务器响应流
		resp.Body.Close()//resp.Body.Close关闭resp的Body流，防止资源泄露
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
			os.Exit(1)
		}
		fmt.Printf("%s", b)//将结果b写出到标准输出流中
	}
}

~~~

这个程序从两个package中导入了函数，net/http和io/ioutil包，`http.Get`函数是创建HTTP请求 的函数，如果获取过程没有出错，那么会在resp这个结构体中得到访问的请求结果。

## Time

在golang中，很多方法接收的时间参数都是time.Duration类型。例如sleep方法，使用



```go
time.Sleep(1 * time.Second)
```

其中`1 * time.Second`的结果就是time.Duration类型。
 但如果想把1换成一个int变量，例如



```go
var num = int(1)
time.Sleep(num * time.Second)
```

就会报错`mismatched types Duration and int`，意思是`num * time.Second`是int类型而不是time.Duration类型。看起来有点滑稽。
 解决办法是先转换int为time.Duration类型：



```go
var num = int(1)
time.Sleep(time.Duration(num) * time.Second)
```

## bufio
bufio 是通过缓冲来提高效率。

io操作本身的效率并不低，低的是频繁的访问本地磁盘的文件。所以bufio就提供了缓冲区(分配一块内存)，读和写都先在缓冲区中，最后再读写文件，来降低访问本地磁盘的次数，从而提高效率。![GO语言基础进阶教程：bufio包](C:\Users\你的姓名\Nutstore\1\我的坚果云\笔记\GO\v2-dff4f93c13d9f5ed6b74944fa89191bc_1440w.jpg)

bufio 封装了io.Reader或io.Writer接口对象，并创建另一个也实现了该接口的对象。

io.Reader或io.Writer 接口实现read() 和 write() 方法，对于实现这个接口的对象都是可以使用这两个方法的。

![img](C:\Users\你的姓名\Nutstore\1\我的坚果云\笔记\GO\v2-39977cf9806264d85d09d4149bce9062_1440w.jpg)

### Reader对象

bufio.Reader 是bufio中对io.Reader 的封装

```go
// Reader implements buffering for an io.Reader object.
type Reader struct {
    buf          []byte
    rd           io.Reader // reader provided by the client
    r, w         int       // buf read and write positions
    err          error
    lastByte     int // last byte read for UnreadByte; -1 means invalid
    lastRuneSize int // size of last rune read for UnreadRune; -1 means invalid
}
```

bufio.Read(p []byte) 相当于读取大小len(p)的内容，思路如下：

1. 当缓存区有内容的时，将缓存区内容全部填入p并清空缓存区
2. 当缓存区没有内容的时候且len(p)>len(buf),即要读取的内容比缓存区还要大，直接去文件读取即可
3. 当缓存区没有内容的时候且len(p)<len(buf),即要读取的内容比缓存区小，缓存区从文件读取内容充满缓存区，并将p填满（此时缓存区有剩余内容）
4. 以后再次读取时缓存区有内容，将缓存区内容全部填入p并清空缓存区（此时和情况1一样）

读取数据：

```go
package main

import (
    "os"
    "fmt"
    "bufio"
)

func main() {
    /*
    bufio:高效io读写
        buffer缓存
        io：input/output

    将io包下的Reader，Write对象进行包装，带缓存的包装，提高读写的效率

        ReadBytes()
        ReadString()
        ReadLine()

     */

     fileName:="/Users/ruby/Documents/pro/a/english.txt"
     file,err := os.Open(fileName)
     if err != nil{
        fmt.Println(err)
        return
     }
     defer file.Close()

     //创建Reader对象
     //b1 := bufio.NewReader(file)
     //1.Read()，高效读取
     //p := make([]byte,1024)
     //n1,err := b1.Read(p)
     //fmt.Println(n1)
     //fmt.Println(string(p[:n1]))

     //2.ReadLine()
     //data,flag,err := b1.ReadLine()
     //fmt.Println(flag)
     //fmt.Println(err)
     //fmt.Println(data)
     //fmt.Println(string(data))

     //3.ReadString()
    // s1,err :=b1.ReadString('\n')
    // fmt.Println(err)
    // fmt.Println(s1)
    //
    // s1,err = b1.ReadString('\n')
    // fmt.Println(err)
    // fmt.Println(s1)
    //
    //s1,err = b1.ReadString('\n')
    //fmt.Println(err)
    //fmt.Println(s1)
    //
    //for{
    //  s1,err := b1.ReadString('\n')
    //  if err == io.EOF{
    //      fmt.Println("读取完毕。。")
    //      break
    //  }
    //  fmt.Println(s1)
    //}

    //4.ReadBytes()
    //data,err :=b1.ReadBytes('\n')
    //fmt.Println(err)
    //fmt.Println(string(data))


    //Scanner
    //s2 := ""
    //fmt.Scanln(&s2)
    //fmt.Println(s2)

    b2 := bufio.NewReader(os.Stdin)
    s2, _ := b2.ReadString('\n')
    fmt.Println(s2)

}
```
### writer对象

bufio.Writer 是bufio中对io.Writer 的封装

```go
// Writer implements buffering for an io.Writer object.
// If an error occurs writing to a Writer, no more data will be
// accepted and all subsequent writes, and Flush, will return the error.
// After all data has been written, the client should call the
// Flush method to guarantee all data has been forwarded to
// the underlying io.Writer.
type Writer struct {
    err error
    buf []byte
    n   int
    wr  io.Writer
}
```

bufio.Write(p []byte) 的思路如下

1. 判断buf中可用容量是否可以放下 p
2. 如果能放下，直接把p拼接到buf后面，即把内容放到缓冲区
3. 如果缓冲区的可用容量不足以放下，且此时缓冲区是空的，直接把p写入文件即可
4. 如果缓冲区的可用容量不足以放下，且此时缓冲区有内容，则用p把缓冲区填满，把缓冲区所有内容写入文件，并清空缓冲区
5. 判断p的剩余内容大小能否放到缓冲区，如果能放下（此时和步骤1情况一样）则把内容放到缓冲区
6. 如果p的剩余内容依旧大于缓冲区，（注意此时缓冲区是空的，情况和步骤3一样）则把p的剩余内容直接写入文件

b.wr 存储的是一个io.writer对象，实现了Write()的接口，所以可以使用b.wr.Write(p) 将p的内容写入文件。

b.flush() 会将缓存区内容写入文件，当所有写入完成后，因为缓存区会存储内容，所以需要手动flush()到文件。

b.Available() 为buf可用容量，等于len(buf) - n。

```go
package main

import (
    "os"
    "fmt"
    "bufio"
)

func main() {
    /*
    bufio:高效io读写
        buffer缓存
        io：input/output

    将io包下的Reader，Write对象进行包装，带缓存的包装，提高读写的效率

        func (b *Writer) Write(p []byte) (nn int, err error)
        func (b *Writer) WriteByte(c byte) error
        func (b *Writer) WriteRune(r rune) (size int, err error)
        func (b *Writer) WriteString(s string) (int, error)

     */

     fileName := "/Users/ruby/Documents/pro/a/cc.txt"
     file,err := os.OpenFile(fileName,os.O_CREATE|os.O_WRONLY,os.ModePerm)
     if err != nil{
        fmt.Println(err)
        return
     }
     defer file.Close()

     w1 := bufio.NewWriter(file)
     //n,err := w1.WriteString("helloworld")
     //fmt.Println(err)
     //fmt.Println(n)
     //w1.Flush() //刷新缓冲区

     for i:=1;i<=1000;i++{
        w1.WriteString(fmt.Sprintf("%d:hello",i))
     }
     w1.Flush()
}

```

## fmt

### 向外输出

- `Print` 最直接的输出

- `Printf `支持格式化输出

- `Printfln`=`Printf`+`\n`

  ------

  

- `Fprint`系列函数会将内容输出到一个`io.Writer`接口类中,我们通常用这个函数往文件中写入内容.

  ```go
  package main
  
  import (
  	"fmt"
  	"os"
  )
  
  func main() {
  
  	// 向标准输出写入内容
  	// os.Stdout 使用 os.Stdout 只能输出到 控制台
  	fmt.Fprintln(os.Stdout, "向标准输出写入内容") // io结束后输出内容 
  	// os.OpenFile(文件路径，文件打开模式，文件权限)
  	fileObj, err := os.OpenFile("./xx.txt", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
  	// 异常捕获
  	if err != nil {
  		fmt.Println("打开文件出错，err:", err)
  		return
  	}
  	name := "北京吴彦祖"
  	// 向打开的文件句柄中写入内容
  	fmt.Fprintf(fileObj, "往文件中写如信息：%s", name)
  }
  
  
  ```

  



## os
### stdout, stdin, stderr

标准输出，标准输入和标准错误

在默认情况下，stdout是行缓冲的，他的输出会放在一个buffer里面，只有到换行的时候，才会输出到屏幕。而stderr是无缓冲的，会直接输出，举例来说就是printf(stdout, "xxxx") 和 printf(stdout, "xxxx\n")，前者会憋住，直到遇到新行才会一起输出。而printf(stderr, "xxxxx")，不管有么有\n，都输出。

stdout -- 标准输出设备 (printf("..")) 同 stdout。
stderr -- 标准错误输出设备

两者默认向屏幕输出。



### Open

os.Open 函数返回两个值。第一个值是被打开的文件( *os.File ），其后被 Scanner 读取。 os.Open 返回的第二个值是内置 error 类型的值。如果 err 等于内置值 nil，那么文件被成功打开

## ioutil
### Readfile
`ioutil.ReadFile` 读取文件只需要传入一个文件名做为 参数，读取成功，会将文件的内容做为一个字节数组返回，如果读取错误，将返回 `error` 信息。
使用 `ReadFile` 读取文件，不需要手动 打开与关闭文件，打开与关闭文件的动作，系统自动帮我们完成。同时，使用 `ReadFile` 读取文件时，只适合读取小文件，不适合读取大文件。