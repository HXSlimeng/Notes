# GoLang

​	Go是一门编译型语言，Go 语言的工具链将源代码及其依赖转换成计算机的机器指令（译注：静 态编译）

​	Go语言最有意思并且最新奇的特性就是对并发编程的支持

​	原生支持Unicode

- Go语言中，所有的函数参数都是值拷贝传入的，函数参数将不再是函数调用时的原始变量

### 工具包

`math/rand`:生成随机数的方法，`rand.Intn(n)`生成0-n的一个随机数

`strconv`:`Itoa`可以将`int`转为`string`

`bufio`:处理输入

```go
input := bufio.NewScanner(os.Stdin)
for input.Scan() {
        counts[input.Text()]++
        for key, v := range counts {
            println(key,v)
        }
        println(len(counts))
    }
```

`os`：文件类型 `*os.File`

​	`os.Stdin`:指向标准输入文件的指针

​	`os.Stdin`、`os.Stdout` 和 `os.Stderr` 分别表示程序的标准输入、标准输出和标准错误流。它们是 `os` 包中的三个常量，分别对应于系统中的标准输入、标准输出和标准错误流。

```go
 f, err := os.Open(arg)
            if err != nil {
                fmt.Fprintf(os.Stderr, "dup2: %v\n", err)
                continue
            }
            countLines(f, counts)
            f.Close()
//可以使用for循环访问文件
for line, n := range counts {
        if n > 1 {
            fmt.Printf("%d\t%s\n", n, line)
        }
    }
```



### 内置函数

- `make(type,len,容量 可选)`:分共配内存空间并初始化 `slice`，`map` 和 `chan` 类型的对象

- `strings`

  - `join`相当于`array.join`,`strings.Join(os.Args,"\n")`

- `map[keyType]valueType`:无序键值对组合

  ```go
  // 遍历 Map
  for k, v := range m {
      fmt.Printf("key=%s, value=%d\n", k, v)
  }
  //删除
  delete(m,'key')
  ```

- `fmt`：`printf`是指`print format`，`println` 是指 `print line`

​			`Fprint` 函数是一个格式化输出函数，它将指定的内容格式化后输出到指定的 `io.Writer` 接口中。这个接口可以是标准输出、文件、网络连接等

​			`Print` 函数是一个简单的输出函数，它将指定的内容直接输出到标准输出（控制台）。

​			`fmt.Sprint`是Go语言中的一个函数，它用于将多个值转换为字符串并拼接在一起

- `bytes`:

```go
import (
	"bytes"
	"fmt"
)
func main() {
	print(intsToString([]int{1, 2, 3}))
}
func intsToString(values []int) string {
    var buf bytes.Buffer
    buf.WriteByte('[')
    for i, v := range values {
        if i > 0 {
            buf.WriteString(", ")
        }
        fmt.Fprintf(&buf, "%d", v)
    }
    buf.WriteByte(']')
    return buf.String()
}
```



### 数据类型

`var`变量声明，声明时直接初始化,如果没有显式初始化则被隐式赋予`零值(zero value)`s数值类型是0，字符串类型是“”

`nil`:相当于其他语言的null

`slice`切片，可以使用`s[m:n]`获取子序列，省略m/n 会默认传入`0`或`len[s]`

`chan`:声明类型 `chan string`指的是传递string类型的信道

指针：指针是一种直接存储了变量的内存地址的数据类型，在go语言中，指针是可见的内存地址，`&`操作符可以返回一个变量的内存地址，并且`*`操作符可以获取指针指向的变量内容，但是在Go语言里没有指针运算，也就是不能像c语言里可以对指针进行加或减操作

### 语法

- 短变量声明`:=`，只能用在函数内部，不能用于包变量

- for循环

  ```go
  for initialization; condition; post {
      // zero or more statements
  }
  //省略initialization、post则为while循环
  for  condition{
      // zero or more statements
  }
  ```

  在数据类型的区间（range）上遍历

  ```go
  for _, arg := range os.Args[1:] {
          s += sep + arg
          sep = " "
      }
  ```

- 空标识符`_`,可以用作for循环占位

- `make` 函数用于`动态分配内存空间`，它会根据提供的参数自动计算所需的空间大小，并返回一个指向该内存区域的指针。使用 `make` 分配内存空间可以确保内存空间被正确初始化，避免因未初始化的内存空间导致的数据竞争或其他问题。

- `switch`:无tag switch

  ```go
  func Signum(x int) int {
      switch {
      case x > 0:
          return +1
      default:
          return 0
      case x < 0:
          return -1
      }
  }
  ```

- 匿名函数

  ```go
  func() (pc [256]byte) {
      for i := range pc {
          pc[i] = pc[i/2] + byte(i&1)
      }
      return
  }()
  ```

- `if`语句行内变量也属于局部变量

  ```go
  if i:=20;i == vari{/* ...*/}
  ```

  

### 测试脚本

- 文件名：module_test.go
- 相关包：regexp、testing

### 杂项

查看go安装目录`go list -f '{{.Target}}' `

`go run src/**.go &`在末尾加入一个`&`可以让程序简单地跑在后台

`go doc`godoc这个工具可以让你直接在本地命令行阅读标准库的文档 example `go doc http.ListenAndServe`

- 获取键盘输入

  ```go
  func main() {
      seen := make(map[string]bool) // a set of strings
      input := bufio.NewScanner(os.Stdin)
      for input.Scan() {
          line := input.Text()
          if !seen[line] {
              seen[line] = true
              fmt.Println(line)
          }
      }
  
      if err := input.Err(); err != nil {
          fmt.Fprintf(os.Stderr, "dedup: %v\n", err)
          os.Exit(1)
      }
  }
  
  ```

  

## 程序结构

### 1. 命名

​	如果一个名i在是大写字母开头的，那么它将会是导出的，可以被外部的包所访问

### 2. 声明

​	`package`说明该文件属于哪个包

### 3. 变量

- 如果不指定变量的初始值，那么将用`零值`初始化变量 `var a type`

  不同数据类型对应的零值

  |                        类型                        | 零值  |
  | :------------------------------------------------: | :---: |
  |                      布尔类型                      | false |
  |                      数值类型                      |   0   |
  |                     字符串类型                     |  “”   |
  | 接口、引用类型（包括slice、指针、map、chan和函数） |  nil  |

  ```go
  i, j := 0, 1
  i, j = j, i // 交换 i 和 j 的值
  ```

- 简短变量声明

  - 声明过的变量将进行赋值操作

  - 必须至少有一个声明的新变量

    ```go
    f, err := os.Open(infile)
    // ...
    f, err := os.Create(outfile) // compile error: no new variables
    ```

- 指针

  ```go
  var x int
  // *x 对应的类型 *int
  
  var x int = 1
  var p = &x //p指针指向 x/ p指针保存了x变量的内存地址
  println(*p) // 1  *p 表示对应p指针指向的变量的值
  *p = 2
  println(x) // 2
  
  func incr(p *int) int {
      *p++ // 非常重要：只是增加p指向的变量的值，并不改变p指针！！！
      return *p
  }
  ```
  
- new 函数
  
  使用`new(T)` 创建一个T类型的匿名变量，初始化为`T类型的零值`，然后返回`变量地址`，返回的指针类型为`*T`

usage`p := new(int)   // p, *int 类型, 指向匿名的 int 变量`

  - 要尽量避免大小为0的类型，可能导致Go语言的垃圾回收器有着不同的行为，`[0]int`与`struct{}`的地址可能相等

- 变量的生命周期，`包变量` 的生命周期与程序的运行周期是一致的，`局部变量`会持续到该变量不被引用为止

- go语言垃圾回收的基本思路：

  ​	从每个包级的变量和每个当前运行函数的每一个局部变量开始，通过指针或引用的访问路径遍历，是否可以找到该变量。如果不存在这样的访问路径，那么说明该变量是不可达的，也就是说它是否存在并不会影响程序后续的计算结果。

### 3. 赋值

1. 元组赋值

   ```go
   x, y = y, x
   
   a[i], a[j] = a[j], a[i]
   ```

### 4.类型

`type 类型名字 底层类型` 名字首字母大写则可以在包外部使用

- 对于每一个类型T，都有一个对应的类型转换操作T(x)，用于将x转为T类型（译注：如果T是指针类型，可能会需要用小括弧包装T，比如`(*int)(0)`）

- 给类型设置方法

  类型参数c出现在了函数名的前面，如下例子表示声明的是`Celsius`类型的一个名叫`String`的方法

  ```go
  func (c Celsius) String() string { return fmt.Sprintf("%g°C", c) }
  ```

### 5. 包和文件

- 包级别的名字，例如在一个`文件声明的类型和常量`，在`同一个包`的其他源文件也是可以直接访问的，就好像所有代码都在一个文件一样

- 包的初始化过程：

  如果包中含有多个.go源文件，它们将按照发给编译器的顺序进行初始化，Go语言的构建工具首先会将.go文件根据文件名排序，然后依次调用编译器编译。

  对于在包级别声明的变量，如果有初始化表达式则用表达式初始化，还有一些没有初始化表达式的，例如某些表格数据初始化并不是一个简单的赋值过程。在这种情况下，我们可以用一个特殊的`init`初始化函数来简化初始化工作。每个文件都可以包含多个init初始化函数

### 6. 作用域

```go
//第二个if语句嵌套在第一个内部，因此第一个if语句条件初始化词法域声明的变量在第二个if中也可以访问。
if x := f(); x == 0 {
    fmt.Println(x)
} else if y := g(x); x == y {
    fmt.Println(x, y)
} else {
    fmt.Println(x, y)
}
fmt.Println(x, y) // compile error: x and y are not visible here

//局部作用域的声明会覆盖全局作用域
var cwd string

func init() {
    cwd, err := os.Getwd() // compile error: unused: cwd
    if err != nil {
        log.Fatalf("os.Getwd failed: %v", err)
    }
}

```

## 基础数据类型

### 1. 整型

>  1 byte 字节 = 8 bit 位,int8 表示 8位也就是1字节
>
> 两位 16进制 相当于 一字节 8 位 

​	有符号整数：int8、int16、int32和int64

​	无符号整数：uint8、uint16、uint32和uint64

​	int、uint 对应特定cpu平台机器字大小的有符号和无符号整数

- `Unicode`字符`rune`类型是和`int32`等价的类型，通常用于表示一个`Unicode`码点
- 同样`byte`也是`uint8`类型的等价类型
- `uintptr`没有指定具体的bit大小但是足以容纳指针,uintptr类型只有在底层编程时才需要
- 16 进制表示`0xFFF`,八进制表示`0666`以0开头

- 算数运算的结果、如果需要更多的`bit`位表示，则超出高位的`bit`位将被丢弃

  ```go
  var u uint8 = 255
  fmt.Println(u, u+1, u*u) // "255 0 1"
  //11111110 00000001
  var i int8 = 127
  fmt.Println(i, i+1, i*i) // "127 -128 1"
  ```

- 可以使用`fmt.Printf`控制输出的进制格式

- 单引号`''`通常来表示`rune`类型，展示`unicode`

- > %之后的`[1]`副词告诉Printf函数再次使用第一个操作数。第二，%后的`#`副词告诉Printf在用%o、%x或%X输出时生成0、0x或0X前缀。

  ```go
  o := 0666
  fmt.Printf("%d %[1]o %#[1]o\n", o) // "438 666 0666"
  x := int64(0xdeadbeef)
  fmt.Printf("%d %[1]x %#[1]x %#[1]X\n", x)
  // Output:
  // 3735928559 deadbeef 0xdeadbeef 0XDEADBEEF
  ```

#### 运算符

​	位运算

一个`x<<n`左移运算等价于乘以$2^n$，一个`x>>n`右移运算等价于除以$2^n$

```go
&      位运算 AND
|      位运算 OR
^      位运算 XOR
&^     位清空（AND NOT）
<<     左移
>>     右移

var x uint8 = 1<<1 | 1<<5
var y uint8 = 1<<1 | 1<<2

fmt.Printf("%08b\n", x) // "00100010", the set {1, 5}
fmt.Printf("%08b\n", y) // "00000110", the set {1, 2}

fmt.Printf("%08b\n", x&y)  // "00000010", the intersection {1}
fmt.Printf("%08b\n", x|y)  // "00100110", the union {1, 2, 5}
fmt.Printf("%08b\n", x^y)  // "00100100", the symmetric difference {2, 5}
fmt.Printf("%08b\n", x&^y) // "00100000", the difference {5}

for i := uint(0); i < 8; i++ {
    if x&(1<<i) != 0 { // membership test
        fmt.Println(i) // "1", "5"
    }
}

fmt.Printf("%08b\n", x<<1) // "01000100", the set {2, 6}
fmt.Printf("%08b\n", x>>1) // "00010001", the set {0, 4}

```

### 2. 浮点数

​	`float64` `float32`,优先使用`float64`,因为float32的有效bit位只有23个，其它的bit位用于指数和符号；当整数大于23bit能表达的范围时，float32的表示将出现误差

```go
for x := 0; x < 8; x++ {
    fmt.Printf("x = %d e^x = %8.3f\n", x, math.Exp(float64(x)))
}
```

> 上面代码打印e的幂，打印精度是小数点后三个小数精度和8个字符宽度：

### 3. 复数

​	`complex64` `complex128`分别对应float32和float64两种浮点数精度

### 4. 字符串	

- 内置的len函数可以返回一个字符串中的==字节数目==（不是rune字符数目）
- 单引号在go语言中表示golang中的rune(int32)类型

- 索引操作s[i]返回第i个字节的字节值，i必须满足0 ≤ i< len(s)条件约束。

  ```go
  s := "hello, world"
  fmt.Println(len(s))     // "12"
  fmt.Println(s[0], s[7]) // "104 119" ('h' and 'w')
  ```

- 字符串可以用==和<进行比较；比较通过逐个字节比较完成的，因此比较的结果是字符串自然编码的顺序。

- 字符串的值是不可变的：一个字符串包含的字节序列永远不会被改变，当然我们也可以给一个字符串变量分配一个新字符串值

- `rune`

#### 字符串面值``` `

```go
\a      响铃
\b      退格
\f      换页
\n      换行
\r      回车
\t      制表符
\v      垂直制表符
\'      单引号（只用在 '\'' 形式的rune符号面值中）
\"      双引号（只用在 "..." 形式的字符串面值中）
\\      反斜杠
```

- 可以通过十六进制或八进制转义在字符串面值中包含任意的字节。一个十六进制的转义形式是`\xhh`，其中两个h表示十六进制数字（大写或小写都可以）。一个八进制转义形式是`\ooo`，包含三个八进制的o数字（0到7），但是不能超过`\377`（译注：对应一个字节的范围，十进制为255

#### 编码

`Unicode码点`:每个符号都分配一个唯一的`Unicode码点`，`Unicode码点`对应Go语言中的`rune`整数类型

Go语言字符串面值中的Unicode转义字符让我们可以通过Unicode码点输入特殊的字符。有两种形式：`\uhhhh`对应16bit的码点值，`\Uhhhhhhhh`对应32bit的码点值，其中h是一个十六进制数字；一般很少需要使用32bit的形式。每一个对应码点的UTF8编码

下面的字母串面值都表示相同的值：

> 各种编码并

```go
"世界"
"\xe4\xb8\x96\xe7\x95\x8c" //A
"\u4e16\u754c"
"\U00004e16\U0000754c"
"%e4%b8%96%e7%95%8c"
```

- "\xe4\xb8\x96\xe7\x95\x8c" 是 UTF-8 编码格式的表示方式。
- "\u4e16\u754c" 是 Unicode 编码格式的表示方式，使用了16进制表示。
- "\U00004e16\U0000754c" 也是 Unicode 编码格式的表示方式，使用了32进制表示。
- "%e4%b8%96%e7%95%8c“是`URL`编码
- assic编码长度为1字节对应类型为int8

```go
import "unicode/utf8"

//字符串包含13个字节，以UTF8形式编码，但是只对应9个Unicode字符
s := "Hello, 世界"
fmt.Println(len(s))                    // "13"
fmt.Println(utf8.RuneCountInString(s)) // "9"

//为了处理这些真实的字符，我们需要一个UTF8解码器。unicode/utf8包提供了该功能，我们可以这样使用：
for i := 0; i < len(s); {
    r, size := utf8.DecodeRuneInString(s[i:])
    fmt.Printf("%d\t%c\n", i, r)
    i += size
}
```

> 每一个UTF8字符解码，不管是显式地调用utf8.DecodeRuneInString解码或是在range循环中隐式地解码，如果遇到一个`错误的UTF8编码输入`，将生成一个特别的Unicode字符`\uFFFD`，在印刷中这个符号通常是一个黑色六角或钻石形状，里面包含一个白色的问号"?"。当程序遇到这样的一个字符，通常是一个危险信号，说明输入并不是一个完美没有错误的UTF8字符串。

#### 字符串和数字的转换

```go
x := 123
y := fmt.Sprintf("%d", x)

fmt.Println(y, strconv.Itoa(x)) // "123 123"
//进制转换
fmt.Println(strconv.FormatInt(int64(x), 2)) // "1111011"

s := fmt.Sprintf("x=%b", x) // "x=1111011"
x, err := strconv.Atoi("123")             // x is an int
y, err := strconv.ParseInt("123", 10, 64) // base 10, up to 64 bits
```

> ParseInt函数的第三个参数是用于指定整型数的大小；例如16表示int16，0则表示int。在任何情况下，返回的结果y总是int64类型，你可以通过强制类型转换将它转为更小的整数类型。

### 常量

- 常量不声明类型可以有更高的计算精度

#### 常量生成器 iota （可以理解为常量）

`iota`从0开始递增 可以嵌入表达式

为第一个声明类型，后续所有变量的类型

```go
type Flags uint

const (
    FlagUp Flags = 1 << iota  1 // is up
    FlagBroadcast             2// supports broadcast access capability
    FlagLoopback              4// is a loopback interface
    FlagPointToPoint          8// belongs to a point-to-point link
    FlagMulticast            16// supports multicast access capability
)
const (
    _ = 1 << (10 * iota)
    KiB // 1024
    MiB // 1048576
    GiB // 1073741824
    TiB // 1099511627776             (exceeds 1 << 32)
    PiB // 1125899906842624
    EiB // 1152921504606846976
    ZiB // 1180591620717411303424    (exceeds 1 << 64)
    YiB // 1208925819614629174706176
)
```

## 复合数据类型

### 数组

1. 特点
   - 数组的长度是固定的
   - 数组的长度是数组类型的一个组成部分，因此`[3]int`和`[4]int`是两种不同的数组类型
   - 可以通过`==` `!=`比较，需要类型相同 且每个元素相等/不相等
   - 可以通过指针直接清零
   - 是一个值类型、赋值或传递会进行一次完整的复制
   - 无法动态增长或缩小

2. 声明方式

   ```go
   //1
   var a [len]Type
   //2
   q := [...]int{1, 2, 3} //数组长度根据初始化值得个数来计算得
   //3
   type Currency int
   
   const (
       USD Currency = iota // 美元
       EUR                 // 欧元
       GBP                 // 英镑
       RMB                 // 人民币
   )
   
   symbol := [...]string{USD: "$", EUR: "€", GBP: "￡", RMB: "￥"}
   
   fmt.Println(RMB, symbol[RMB]) // "3 ￥"
   //4
   r := [...]int{99: -1} //长度为100 最后一个元素为 -1
   ```

3. 在Go语言中，直接操作数组和操作数组的指针有一些区别。
   1. 直接操作数组：当你直接操作数组时，你实际上是在对数组进行值拷贝。这意味着对数组的修改不会影响原始数组。

   2. 操作数组的指针：当你操作数组的指针时，你可以直接访问和修改数组的元素，而不需要进行值拷贝。通过指针，你可以在函数之间共享数组，并且对数组的修改会影响到原始数组。

      ```go
      package main
      
      import "fmt"
      
      func modifyArray(arr [3]int) {
          arr[0] = 100
      }
      
      func modifyArrayPtr(arrPtr *[3]int) {
          arrPtr[0] = 100
      }
      
      func main() {
          arr := [3]int{1, 2, 3}
      
          fmt.Println("Original array:", arr)
          //Original array: [1 2 3]
      
          modifyArray(arr)
          fmt.Println("After modifyArray:", arr)
          //After modifyArray: [1 2 3]
      
          modifyArrayPtr(&arr)
          fmt.Println("After modifyArrayPtr:", arr)
          //After modifyArrayPtr: [100 2 3]
      }
      
      
      
      ```

### 切片Slice

1. 特点
   - 变长的序列，每个元素都有相同的类型
   - 是引用类型，在赋值或传递给函数时只会复制指向底层数组的指针，不会复制底层数组的内容
   - 内置的`len`和`cap`函数分别返回`slice`的长度和容量
   - 指针指向第一个slice元素对应的底层数组元素的地址
   - `copy`方法可以方便的将一个`slice`复制到另一个相同类型的`slice`

2. 声明 不定长

   ```go
   var runes []rune
   var test []string{1,2,3,4}
   ```

   ![](https://gopl-zh.github.io/images/ch4-01.png)

3. `append`函数用于给`slice`追加元素

4. 使用`make`创建`slice`

   - 可以指定切片的长度和容量
   - 可以初始化各元素为零值
   - 返回引用

5. `slice`方法实现

   ```go
   //pop
   stack = stack[:len(stack)-1] 
   //删除中间的元素
   func remove(slice []int, i int) []int {
       copy(slice[i:], slice[i+1:])
       return slice[:len(slice)-1]
   }
   
   ```


6. 可以使用`sort.Strings([]string)` 对元素为`string`类型的`slice`切片进行排序

### Map

特点：

- key有相同的类型，val有相同的类型
- 元素不可以取址

声明：

```go
//make函数创建语法
ages := make(map[string]int)

//map字面值语法
ages := map[string]int{
    "alice":   31,
    "charlie": 34,
}
//判断该key是否存在
if _, ok := ages["test3"]; !ok {/*。。。*/}
//删除元素
delete(ages, "alice") 
```

### struct

特点

- 一个命名为S的结构体类型将不能再包含S类型的成员：因为一个聚合的值不能包含它自身。（该限制同样适用于数组。）
- 但是S类型的结构体可以包含`*S`指针类型的成员，
- 可比较，可以用于map的key类型
- 匿名嵌入

声明：

```go
//1. 
//如果相邻的成员类型如果相同的话可以被合并到一行
type Employee struct {
    ID            int
    Name, Address string
    DoB           time.Time
    Position      string
    Salary        int
    ManagerID     int
}

//实现链表数据结构
type tree struct {
    value       int
    left, right *tree
}

//2. 结构体字面值 按顺序赋值
type Point struct{ X, Y int }
p := Point{1, 2}
p["X"] //1

//3.因为结构体通常通过指针处理，可以用下面的写法来创建并初始化一个结构体变量，并返回结构体的地址：
pp := &Point{1, 2}
//等价于
pp := new(Point)
*pp = Point{1, 2}

//匿名嵌入
type Circle struct {
    Point
    Radius int
}

type Wheel struct {
    Circle
    Spokes int
}
var w Wheel

w.Raduis = 20 //可以直接赋值

```

### json

将go语言中的结构体`slice`转为`JSON`的过程叫`编组(marshaling)`

将go语言数据转换为`JSON` `json.Marshal` ,反之使用 `json.Unmarshal`

```go
//紧凑形式
data, err := json.Marshal(movies)
if err != nil {
    log.Fatalf("JSON marshaling failed: %s", err)
}
fmt.Printf("%s\n", data)
//生成整齐缩进的输出
data, err := json.MarshalIndent(movies, "", "    ")

type Movie struct {
		Title  string
		Year   int  `json:"released"` //生成Json时 Year字段将被替换为 released
		Color  bool `json:"color,omitempty"` //omitempty 表示该字段为空 时不生成该字段
		Actors []string
	}
// 将JSON转为 go语言Struct
var titles []struct{ Title string }
if err := json.Unmarshal(data, &titles); err != nil {
    log.Fatalf("JSON unmarshaling failed: %s", err)
}
fmt.Println(titles) // "[{Casablanca} {Cool Hand Luke} {Bullitt}]"
package github

import "time"

const IssuesURL = "https://api.github.com/search/issues"

type IssuesSearchResult struct {
    TotalCount int `json:"total_count"`
    Items          []*Issue
}

type Issue struct {
    Number    int
    HTMLURL   string `json:"html_url"`
    Title     string
    State     string
    User      *User
    CreatedAt time.Time `json:"created_at"`
    Body      string    // in Markdown format
}

type User struct {
    Login   string
    HTMLURL string `json:"html_url"`
}

```

流式解码器 `json.NewDecoder(resp.Body).Decode(&result)`

### 文本和html模板

