# GoLang（Go 语言圣经）

[【本书连接】](https://gopl-zh.github.io/)

Go 是一门编译型语言，Go 语言的工具链将源代码及其依赖转换成计算机的机器指令（译注：静 态编译）

Go 语言最有意思并且最新奇的特性就是对并发编程的支持

`TinyGo`是一个用于嵌入式设备和WebAssembly的Go编译器。它是Go语言的一个子集，旨在提供更小的二进制文件大小和更低的内存占用。TinyGo的目标是在资源受限的环境中运行Go代码，例如微控制器、物联网设备和浏览器中的WebAssembly模块。它支持大部分的Go语言特性，并提供了与标准Go编译器兼容的语法和API。

`Scoop`是一个针对Windows操作系统的命令行软件包管理器。它可以帮助用户在Windows上轻松地安装、更新和管理各种开源软件包

原生支持 Unicode

- Go 语言中，所有的函数参数都是值拷贝传入的，函数参数将不再是函数调用时的原始变量
- `go.sun`:管理项目的模块依赖项的版本信息
- 内存分配发生在以下情况下：

  - 使用`new`关键字创建新的对象时，会在堆上分配内存。
  - 使用字面量或复合字面量（composite literal）初始化变量时，也会在堆上分配内存。
- 拷贝操作发生在以下情况下：

  - 将一个变量赋值给另一个变量时，会进行浅拷贝。这意味着只会复制指针，而不会复制对象本身。修改其中一个变量的值会影响到另一个变量。
  - 传递参数给函数时，会进行参数拷贝。如果参数是一个指针类型，那么拷贝的是指针，而不是指针指向的对象。
- 在Go语言中，相对路径是相`对于当前工作目录`的路径。当前工作目录是您在`运行Go程序时所在的目录`。

```go
package main

import (
    "fmt"
    "os"
    "path/filepath"
)

func main() {
    wd, err := os.Getwd()
    if err != nil {
        fmt.Println("Failed to get current working directory:", err)
        return
    }

    relativePath := "path/to/file.txt"
    absolutePath := filepath.Join(wd, relativePath)

    fmt.Println("Absolute path:", absolutePath)
}
```



### 工具包

`math/rand`:生成随机数的方法，`rand.Intn(n)`生成 0-n 的一个随机数

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

`os.Stdin`:指向标准输入文件的指针

`os.Stdin`、`os.Stdout` 和 `os.Stderr` 分别表示程序的标准输入、标准输出和标准错误流。它们是 `os` 包中的三个常量，分别对应于系统中的标准输入、标准输出和标准错误流。

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

- `log`

  - `Fatalf`:默认会在错误信息之前输出时间信息
  -

- `make(type,len,容量 可选)`:分共配内存空间并初始化 `slice`，`map` 和 `chan` 类型的对象

- `strings`

  - `join`相当于`array.join`,`strings.Join(os.Args,"\n")`
  - `Map`对传入字符串的每个字符使用函数

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

`Fprint` `F`指的是`File` 函数是一个格式化输出函数，它将指定的内容格式化后输出到指定的 `io.Writer` 接口中。这个接口可以是标准输出、文件、网络连接等

`Print` 函数是一个简单的输出函数，它将指定的内容直接输出到标准输出（控制台）。

`fmt.Sprint`是 Go 语言中的一个函数，它用于将多个值转换为字符串并拼接在一起，返回新字符串

`fmt.Errorf`:将错误格式化

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

| formatString | 说明                                   |
| ------------ | -------------------------------------- |
| %v：         | 默认格式，根据值的类型进行格式化输出   |
| %d：         | 十进制整数。                           |
| %f：         | 浮点数。                               |
| %s：         | 字符串。                               |
| %t：         | 布尔值。                               |
| %p：         | 指针值。                               |
| %x：         | 十六进制表示的整数。                   |
| %b：         | 二进制表示的整数。                     |
| %10d：       | 指定输出宽度为 10 个字符的十进制整数。 |
| %.2f：       | 指定浮点数的小数点后保留 2 位。        |
| %s：         | 指定字符串的输出宽度。                 |
| %c:		   |将整数值解释为对应的Unicode字符 		|

`io.EOF`在可读数据没有输入的时候返回

[golang 说明文档](https://pkg.go.dev/fmt)

### 数据类型

`var`变量声明，声明时直接初始化,如果没有显式初始化则被隐式赋予`零值(zero value)`s 数值类型是 0，字符串类型是“”

`nil`:相当于其他语言的 null

`slice`切片，可以使用`s[m:n]`获取子序列，省略 m/n 会默认传入`0`或`len[s]`

`chan`:声明类型 `chan string`指的是传递 string 类型的信道

指针：指针是一种直接存储了变量的内存地址的数据类型，在 go 语言中，指针是可见的内存地址，`&`操作符可以返回一个变量的内存地址，并且`*`操作符可以获取指针指向的变量内容，但是在 Go 语言里没有指针运算，也就是不能像 c 语言里可以对指针进行加或减操作

### 语法

- 短变量声明`:=`，只能用在函数内部，不能用于包变量

- for 循环

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

- 空标识符`_`,可以用作 for 循环占位

- `make` 函数用于`动态分配内存空间`，它会根据提供的参数自动计算所需的空间大小，并返回一个指向该内存区域的指针。使用 `make` 分配内存空间可以确保内存空间被正确初始化，避免因未初始化的内存空间导致的数据竞争或其他问题。

- `switch`:无 tag switch

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

查看 go 安装目录`go list -f '{{.Target}}' `

`go run src/**.go &`在末尾加入一个`&`可以让程序简单地跑在后台

`go doc`godoc 这个工具可以让你直接在本地命令行阅读标准库的文档 example `go doc http.ListenAndServe`

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

如果一个名 i 在是大写字母开头的，那么它将会是导出的，可以被外部的包所访问

### 2. 声明

`package`说明该文件属于哪个包

### 3. 变量

- 如果不指定变量的初始值，那么将用`零值`初始化变量 `var a type`

  不同数据类型对应的零值

  |                         类型                         | 零值  |
  | :--------------------------------------------------: | :---: |
  |                       布尔类型                       | false |
  |                       数值类型                       |   0   |
  |                      字符串类型                      |  “”   |
  | 接口、引用类型（包括 slice、指针、map、chan 和函数） |  nil  |

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

  使用`new(T)` 创建一个 T 类型的匿名变量，初始化为`T类型的零值`，然后返回`变量地址`，返回的指针类型为`*T`

usage`p := new(int)   // p, *int 类型, 指向匿名的 int 变量`

- 要尽量避免大小为 0 的类型，可能导致 Go 语言的垃圾回收器有着不同的行为，`[0]int`与`struct{}`的地址可能相等

- 变量的生命周期，`包变量` 的生命周期与程序的运行周期是一致的，`局部变量`会持续到该变量不被引用为止

- go 语言垃圾回收的基本思路：

  从每个包级的变量和每个当前运行函数的每一个局部变量开始，通过指针或引用的访问路径遍历，是否可以找到该变量。如果不存在这样的访问路径，那么说明该变量是不可达的，也就是说它是否存在并不会影响程序后续的计算结果。

### 3. 赋值

1. 元组赋值

   ```go
   x, y = y, x

   a[i], a[j] = a[j], a[i]
   ```

### 4.类型

`type 类型名字 底层类型` 名字首字母大写则可以在包外部使用

- 对于每一个类型 T，都有一个对应的类型转换操作 T(x)，用于将 x 转为 T 类型（译注：如果 T 是指针类型，可能会需要用小括弧包装 T，比如`(*int)(0)`）

- 给类型设置方法

  类型参数 c 出现在了函数名的前面，如下例子表示声明的是`Celsius`类型的一个名叫`String`的方法

  ```go
  func (c Celsius) String() string { return fmt.Sprintf("%g°C", c) }
  ```

### 5. 包和文件

- 包级别的名字，例如在一个`文件声明的类型和常量`，在`同一个包`的其他源文件也是可以直接访问的，就好像所有代码都在一个文件一样

- 包的初始化过程：

  如果包中含有多个.go 源文件，它们将按照发给编译器的顺序进行初始化，Go 语言的构建工具首先会将.go 文件根据文件名排序，然后依次调用编译器编译。

  对于在包级别声明的变量，如果有初始化表达式则用表达式初始化，还有一些没有初始化表达式的，例如某些表格数据初始化并不是一个简单的赋值过程。在这种情况下，我们可以用一个特殊的`init`初始化函数来简化初始化工作。每个文件都可以包含多个 init 初始化函数
  
- 同目录下的`go`文件必须属于同一个`package`

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

> 1 byte 字节 = 8 bit 位,int8 表示 8 位也就是 1 字节
>
> 两位 16 进制 相当于 一字节 8 位

有符号整数：int8、int16、int32 和 int64

无符号整数：uint8、uint16、uint32 和 uint64

int、uint 对应特定 cpu 平台机器字大小的有符号和无符号整数

- `Unicode`字符`rune`类型是和`int32`等价的类型，通常用于表示一个`Unicode`码点
- 同样`byte`也是`uint8`类型的等价类型
- `uintptr`没有指定具体的 bit 大小但是足以容纳指针,uintptr 类型只有在底层编程时才需要
- 16 进制表示`0xFFF`,八进制表示`0666`以 0 开头

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

- > %之后的`[1]`副词告诉 Printf 函数再次使用第一个操作数。第二，%后的`#`副词告诉 Printf 在用%o、%x 或%X 输出时生成 0、0x 或 0X 前缀。

  ```go
  o := 0666
  fmt.Printf("%d %[1]o %#[1]o\n", o) // "438 666 0666"
  x := int64(0xdeadbeef)
  fmt.Printf("%d %[1]x %#[1]x %#[1]X\n", x)
  // Output:
  // 3735928559 deadbeef 0xdeadbeef 0XDEADBEEF
  ```

#### 运算符

位运算

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

`float64` `float32`,优先使用`float64`,因为 float32 的有效 bit 位只有 23 个，其它的 bit 位用于指数和符号；当整数大于 23bit 能表达的范围时，float32 的表示将出现误差

```go
for x := 0; x < 8; x++ {
    fmt.Printf("x = %d e^x = %8.3f\n", x, math.Exp(float64(x)))
}
```

> 上面代码打印 e 的幂，打印精度是小数点后三个小数精度和 8 个字符宽度：

### 3. 复数

`complex64` `complex128`分别对应 float32 和 float64 两种浮点数精度

### 4. 字符串

- 内置的 len 函数可以返回一个字符串中的==字节数目==（不是 rune 字符数目）
- 单引号在 go 语言中表示 golang 中的 rune(int32)类型

- 索引操作 s[i]返回第 i 个字节的字节值，i 必须满足 0 ≤ i< len(s)条件约束。

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

- 可以通过十六进制或八进制转义在字符串面值中包含任意的字节。一个十六进制的转义形式是`\xhh`，其中两个 h 表示十六进制数字（大写或小写都可以）。一个八进制转义形式是`\ooo`，包含三个八进制的 o 数字（0 到 7），但是不能超过`\377`（译注：对应一个字节的范围，十进制为 255

#### 编码

`Unicode码点`:每个符号都分配一个唯一的`Unicode码点`，`Unicode码点`对应 Go 语言中的`rune`整数类型

Go 语言字符串面值中的 Unicode 转义字符让我们可以通过 Unicode 码点输入特殊的字符。有两种形式：`\uhhhh`对应 16bit 的码点值，`\Uhhhhhhhh`对应 32bit 的码点值，其中 h 是一个十六进制数字；一般很少需要使用 32bit 的形式。每一个对应码点的 UTF8 编码

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
- "\u4e16\u754c" 是 Unicode 编码格式的表示方式，使用了 16 进制表示。
- "\U00004e16\U0000754c" 也是 Unicode 编码格式的表示方式，使用了 32 进制表示。
- "%e4%b8%96%e7%95%8c“是`URL`编码
- assic 编码长度为 1 字节对应类型为 int8

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

> 每一个 UTF8 字符解码，不管是显式地调用 utf8.DecodeRuneInString 解码或是在 range 循环中隐式地解码，如果遇到一个`错误的UTF8编码输入`，将生成一个特别的 Unicode 字符`\uFFFD`，在印刷中这个符号通常是一个黑色六角或钻石形状，里面包含一个白色的问号"?"。当程序遇到这样的一个字符，通常是一个危险信号，说明输入并不是一个完美没有错误的 UTF8 字符串。

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

> ParseInt 函数的第三个参数是用于指定整型数的大小；例如 16 表示 int16，0 则表示 int。在任何情况下，返回的结果 y 总是 int64 类型，你可以通过强制类型转换将它转为更小的整数类型。

### 常量

- 常量不声明类型可以有更高的计算精度

#### 常量生成器 iota （可以理解为常量）

`iota`从 0 开始递增 可以嵌入表达式

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

3. 在 Go 语言中，直接操作数组和操作数组的指针有一些区别。

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

      ```

      ```

### 切片 Slice

1. 特点

   - 变长的序列，每个元素都有相同的类型
   - 是引用类型，在赋值或传递给函数时只会复制指向底层数组的指针，不会复制底层数组的内容
   - 内置的`len`和`cap`函数分别返回`slice`的长度和容量
   - 指针指向第一个 slice 元素对应的底层数组元素的地址
   - `copy`方法可以方便的将一个`slice`复制到另一个相同类型的`slice`

2. 声明 不定长

   ```go
   var runes []rune
   var test []string{1,2,3,4}
   ```

   ![](https://gopl-zh.github.io/images/ch4-01.png)

3. `append`函数用于给`slice`追加元素）（如若`cap`不足 append 方法会自动扩充该容量）

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

- key 有相同的类型，val 有相同的类型
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

- 一个命名为 S 的结构体类型将不能再包含 S 类型的成员：因为一个聚合的值不能包含它自身。（该限制同样适用于数组。）
- 但是 S 类型的结构体可以包含`*S`指针类型的成员，
- 可比较，可以用于 map 的 key 类型
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

将 go 语言中的结构体`slice`转为`JSON`的过程叫`编组(marshaling)`

将 go 语言数据转换为`JSON` `json.Marshal` ,反之使用 `json.Unmarshal`

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

### 文本和 html 模板

使用：

- 使用 `template.Must` 辅助函数避免模板解析失败
- 使用 `template.New(name)`创建并返回一个模板
- 使用 `Func` 方法将自定义函数注册到模板中
- 调用 `Parse` 函数分析模板
- 使用 `report.Execute(os.Stdout, result)` 将结果输出到控制台
- 模板中`{{range .Items}}`和`{{end}}`对应一个循环`action`

```go
type IssuesSearchResult struct {
    TotalCount int `json:"total_count"`
    Items          []*Issue
    IncompleteResults bool `json:"incomplete_results"`
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
const templ = `{{.TotalCount}} issues:
{{range .Items}}----------------------------------------
Number: {{.Number}}
User:   {{.User.Login}}
Title:  {{.Title | printf "%.64s"}}
Age:    {{.CreatedAt | daysAgo}} days
{{end}}
{{.IncompleteResults}}
`
func main() {
    report := template.Must(template.New("report").Funcs(template.FuncMap{"daysAgo": daysAgo}).Parse(templ))
    searchBox := []string{"https://github.com/wangduanduan/jsplumb-chinese-tutorial"}
    result, err := SearchIssues(searchBox)
    if err != nil {
        log.Fatal(err)
    }
    report.Execute(os.Stdout, result)
}
```

## 函数

特点：

- 实参通过值得方式传递，如果实参包括引用类型，如指针，slice(切片)、map、function、channel 等类型，实参可能会由于函数的间接引用被修改
- 没有函数体的函数声明、表示该函数不是以 Go 实现的，这样的声明定义了函数签名

  ```go
  package math
  func Sin(x float64) float //implemented in assembly language
  ```

- 函数也有闭包

特殊返回值命名

```go
func tf(x,y int)(sum int)  {
    sum = x + y
    return
}
```

> 该情况下 返回值被声明为一个`sum`的零值

```go
//不分成两步 函数字面量无法与visitAll绑定
visitAll := func(items []string) {
    // ...
    visitAll(m[item]) // compile error: undefined: visitAll
    // ...
}
```

循环或者 rang 中使用的局部变量都是同一个 每一次循环更新该变量的值

```go
var rmdirs []func()
for _, dir := range tempDirs() {
    //正确使用方法创建一个新的局部变量
    //var di = dir
    os.MkdirAll(dir, 0755)
    rmdirs = append(rmdirs, func() {
        //会使用最后一个dir值
        os.RemoveAll(dir) // NOTE: incorrect!
    })
}
```

### 可变参数

`interface`表示函数的最后一个参数可以接收任意类型

```go
func errorf(linenum int, format string, args ...interface{}) {
    fmt.Fprintf(os.Stderr, "Line %d: ", linenum)
    fmt.Fprintf(os.Stderr, format, args...)
    fmt.Fprintln(os.Stderr)
}
linenum, name := 12, "count"
errorf(linenum, "undefined: %s", name) // "Line 12: undefined: count"

```

### Deferred 函数

defer 语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。通过 defer 机制，不论函数逻辑多复杂，都能保证在任何执行路径下，资源被释放。释放资源的 defer 应该直接跟在请求资源的语句后。

在 Go 语言中，`defer`关键字用于延迟执行某个函数或方法的调用，直到==包围它的函数或方法返回之前==。`defer`语句通常用于在函数或方法执行结束后进行一些清理工作，例如关闭文件、释放资源、解锁互斥锁等。它主要用于简化代码的编写和维护，确保关键的清理操作不会被遗漏。

`defer`语句的执行顺序是后进先出（LIFO）的，也就是说，最后一个`defer`语句将最先执行，而第一个`defer`语句将最后执行。当包围`defer`语句的函数或方法返回时，所有延迟执行的函数调用会按照它们定义的顺序逆序执行。

- 在命名返回值的前提下 可以打印返回值

  ```go
  func double(x int) (result int) {
      defer func() { fmt.Printf("double(%d) = %d\n", x,result) }()
      return x + x
  }
  _ = double(4)
  // Output:
  // "double(4) = 8"
  ```

- 在以上前提下可以甚至可以直接修改返回值

  ```go
  func triple(x int) (result int) {
      defer func() { result += x }()
      return double(x)
  }
  fmt.Println(triple(4)) // "12"

  ```

- 注意：以下这种情况会导致错误

  ```go
  //f.Close 只会在包含此循环的函数体执行完再执行Close ，导致系统的文件描述符耗尽，因为在所有文件都被处理之前，没有文件会被关闭
  for _, filename := range filenames {
      f, err := os.Open(filename)
      if err != nil {
          return err
      }
      defer f.Close() // NOTE: risky; could run out of file descriptors
      // ...process f…
  }
  //改进 封装进单独的函数
  for _, filename := range filenames {
      if err := doFile(filename); err != nil {
          return err
      }
  }
  func doFile(filename string) error {
      f, err := os.Open(filename)
      if err != nil {
          return err
      }
      defer f.Close()
      // ...process f…
  }

  ```

### Panic 异常

- `Panic`异常发生时，程序会终端运行，立即执行在该`goroutine`中被延迟的函数（defer）机制，然后程序崩溃并输出日志信息
- 使用`panic`函数可以主动抛出错误` panic(fmt.Sprintf("invalid suit %q", s)) // Joker?`
- 在 Go 的 panic 机制中，延迟函数`(defer)`的调用在释放堆栈信息之前

### Recover 捕获异常

如果在 deferred 函数中调用了内置函数`recover`，并且定义该 defer 语句的函数发生了 panic 异常，recover 会使程序从 panic 中恢复，并返回 panic value

```go
func Parse(input string) (s *Syntax, err error) {
    defer func() {
        if p := recover(); p != nil {
            err = fmt.Errorf("internal error: %v", p)
        }
    }()
    // ...parser...
}
```

 `recover`函数帮助`Parse`从`panic`中恢复。在`deferred`函数内部，`panic value`被附加到错误信息中；并用`err`变量接收错误信息，返回给调用者。我们也可以通过调用`runtime.Stack`往错误信息中添加完整的堆栈调用信息。

## 方法

方法声明

`func (p 类型) 方法名(q Point) float64 { /*。。。*/ }`

`(p 类型)`称为方法的接收器

特点：

- 没有 this 或者 self 作为接收器，以上示例中可以调用`p`访问该类型的一些属性及方法

### 基于指针对象的方法

```go
//声明
func (p *Point) ScaleBy(factor float64) {
    p.X *= factor
    p.Y *= factor
}
//使用
p.ScaleBy()//p 被默认转为&p去调用该方法
```

- 在声明方法时，如果一个类型名本身是一个指针的话，是不允许其出现在接收器中的

  ```go
  type P *int
  func (P) f() { /* ... */ } // compile error: invalid receiver type
  ```

- 编译器会隐式地帮我们用`&`取址去调用指针对象方法，这种简写方法只适用于“变量”，包括 struct 里的字段比如 p.X，以及 array 和 slice 内的元素比如 perim[0]

- 注：不管你的 method 的 receiver 是指针类型还是非指针类型，都是可以通过指针/非指针类型进行调用的，编译器会帮你做类型转换。

- 注：在声明一个 method 的 receiver 该是指针还是非指针类型时，你需要考虑两方面的因素，第一方面是这个`对象本身是不是特别大`，如果声明为非指针变量时，调用会产生一次`拷贝`；第二方面是如果你用指针类型作为 receiver，那么你一定要注意，这种指针类型指向的始终是一块内存地址，就算你对其进行了拷贝。

- `nil`也是一个合法的接收器类型

### 封装

- 首字母小写：私有变量 首字符大写：公有变量 (在其他 package 引入访问时的效果)

## 接口

空接口类型：

```go
var any interface{}
any = true
any = 12.34
any = "hello"
any = map[string]int{"one": 1}
any = new(bytes.Buffer)
```

### flag.value接口



```go
//定义
var period = flag.Duration("period", 1*time.Second, "sleep period")

//使用
func main(){
	flag.Parse() //解析行内参数
    fmt.Printf("Sleeping for %v...", *period)
    time.Sleep(*period)
    fmt.Println()
}

//cmd
go run . -period 50ms
```

- 可以自定义实现一个类flag方法获取命令行参数

  example：

  ```go
  func CelsiusFlag(name string, value Celsius, usage string) *Celsius {
      f := celsiusFlag{value}
      flag.CommandLine.Var(&f, name, usage) //可以使用该方法注册一个flag
      return &f.Celsius
  }
  ```

### sort.interface接口

### http.handle

- 可以使用`ServeMux`来简化URL和handlers的联系

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	db := database{"shoes": 50, "socks": 5}
	mux := http.NewServeMux()
	mux.Handle("/list", http.HandlerFunc(db.list))
	mux.Handle("/price", http.HandlerFunc(db.price))
	log.Fatal(http.ListenAndServe("localhost:8000", mux))
}
type dollars float32
func (d dollars) String() string { return fmt.Sprintf("$%.2f", d) }

type database map[string]dollars

func (db database) list(w http.ResponseWriter, req *http.Request) {
	for item, price := range db {
		fmt.Fprintf(w, "%s: %s\n", item, price)
	}
}

func (db database) price(w http.ResponseWriter, req *http.Request) {
	item := req.URL.Query().Get("item")
	price, ok := db[item]
	if !ok {
		w.WriteHeader(http.StatusNotFound) // 404
		fmt.Fprintf(w, "no such item: %q\n", item)
		return
	}
	fmt.Fprintf(w, "%s\n", price)
}

```

### error接口

### 类型断言

1. 语法

   `f := val.(type)`:校验错误会抛出`panic错误`

   `f, ok := val.(float64)`:不会抛出错误，转换成功与否会以布尔值形式存在`ok`中

2. 实践

   ```go
   if w, ok := w.(*os.File); ok {
       // ...use w...
   }
   ```

3. 基于类型断言判断错误

   `fmt.Println(os.IsNotExist(err))`,
   
4. 可以通过类型断言判断变量是否有某个方法，从而决定其行为

   ```go
   type stringWriter interface {
           WriteString(string) (n int, err error)
       }
       if sw, ok := w.(stringWriter); ok {
           return sw.WriteString(s) // avoid a copy
       }
   ```

### 类型分支

可以使用`switch x.(type)`判断类型

```go
var x interface{}

switch  x := x.(type)//x.(type) {
case nil:       // ...
case int, uint: // ...
case bool:      // ...
case string:    // ...
default:        // ...
}
```

## Goroutines和Channels

### Goroutines

可以理解为执行一个使用go修饰的函数时 其执行可以与其他函数体并行执行

在Go语言中，如果主协程（即`main`函数所在的协程）执行完毕后，即使其他协程还没有完成，程序也会立即退出，如果希望等待所有的goroutine完成后再退出程序，可以使用`sync.WaitGroup`来实现

### Channel

1. 线程安全：channel是线程安全的，多个协程可以同时读写一个channel，而不会发生数据竞争的问题(go在Channel内部实现了锁机制)
2. 阻塞式发送接收：当一个协程向一个channel发送数据时，如果channel已经满了，发送操作会被阻塞，直到有其他协程从channel中取走了数据
3. 顺序性：先存x再y，则取先取x再y
4. 可以关闭：关闭后可以取无法向其中发送，关闭`channel`可以避免内存泄漏
5. 缓冲区大小：channel可以带有一个缓冲区，用于存储一定量的数据。如果缓冲区已经满了，发送操作会被阻塞，直到有其他协程从channel中取走了数据；`ch := make(chan int, 10)`

![](https://img-blog.csdn.net/20180825194734104?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Nnc2d5NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 不带缓存的Channel

- `channel`接收数据时如果`channel`中没有数据可供接收，接收操作会被阻塞，直到有其他协程向`channel`中发送了数据

  

```go
ch := make(chan int) // ch has type 'chan int'
ch <- x  // a send statement
x = <-ch // a receive expression in an assignment statement
<-ch     // 不使用接收参数
close(ch)

//make 如果第二个参数大于0 就是带缓存的channel
ch = make(chan int)    // 无缓存 channel
ch = make(chan int, 0) // 无 channel
ch = make(chan int, 3) // 容量为3 的channel
```

#### 串联的Channels（pipeline） 单方向Channel

```go
func counter(out chan<- int) { //out只发送 chan <- int
    for x := 0; x < 100; x++ {
        out <- x
    }
    close(out)
}

func squarer(out chan<- int, in <-chan int) { //in只接收 <-chan int
    for v := range in {
        out <- v * v
    }
    close(out)
}

func printer(in <-chan int) {
    for v := range in {
        fmt.Println(v)
    }
}

func main() {
    naturals := make(chan int)
    squares := make(chan int)
    go counter(naturals)
    go squarer(squares, naturals)
    printer(squares)
}
```

#### 带缓存的Channel

1. 向`channel`发送时，如果`channel`容量满，则发生阻塞
   - 查看ch容量`cap(ch)`
   - 查看ch有效元素个数`len(ch)`

可以使用`sync.WaitGroup`统计`goroutines`的数量

### 并发的循环

```go
func makeThumbnails6(filenames <-chan string) int64 {
    resChannel := make(chan int64)
    var wg sync.WaitGroup // number of working goroutines
    for f := range filenames {
        wg.Add(1)
        go func(f string) {
            defer wg.Done()
            resChannel <- manage()//。。。
        }(f)
    }

    // closer
    go func() {
        wg.Wait()
        close(resChannel)
    }()

    var total int64
    for size := range resChannel {
        total += size
    }
    return total
}
```

### 基于Select的多路复用

```go
select {
case <-ch1:
    // ...
case x := <-ch2:
    // ...use x...
case ch3 <- y:
    // ...
default:
    // ...
}
```

## 基于共享变量的并发

### 互斥锁`Sync.Mutex`

```go
var mu sync.Mutex

func Deposit(amount int) {
    mu.Lock()
    balance = balance + amount
    mu.Unlock()
}
```

- 无法对变量进行重入锁，否则会导致程序死锁

### 读写锁`sync.RWMutex`

​	读锁（也称为共享锁）可以被多个读操作同时获取，但是写操作需要等待所有读操作完成后才能获取写锁。这种机制可以实现多个并发的读操作，而不会互相影响。

​	写锁（也称为排他锁）只能被一个写操作获取，当一个写锁被获取时，其他读操作和写操作都需要等待该写锁释放后才能继续执行。这种机制可以确保在写操作进行时，没有其他读操作或写操作可以同时进行。

### `sync.Once`惰性初始化

每一次对`Do(loadIcons)`的调用都会锁定`mutex`，并会检查boolean变量（译注：Go1.9中会先判断boolean变量是否为1(true)，只有不为1才锁定mutex，不再需要每次都锁定mutex）

```go
var loadIconsOnce sync.Once
var icons map[string]image.Image
// Concurrency-safe.
func Icon(name string) image.Image {
    loadIconsOnce.Do(loadIcons)
    return icons[name]
}
```

### 竞争条件检测

​	在Go语言中，`go build -race` 命令用于构建一个启用了数据竞争检测的可执行程序。数据竞争是多个goroutine并发访问共享变量时可能发生的一种情况，它会导致不确定的行为和bug。

当使用 `-race` 参数进行构建时，Go编译器会插入额外的代码来检测数据竞争。这些额外的代码会记录每个共享变量的访问情况，并在发现潜在的数据竞争时触发一个panic。

通过在构建过程中启用数据竞争检测，可以帮助开发人员找出潜在的并发问题，并更好地理解和调试并发程序。

请注意，使用 `-race` 参数构建的程序可能会有额外的开销，并且会导致执行速度变慢。因此，通常建议在开发和测试阶段使用 `-race` 参数进行构建，以便及早发现并修复潜在的数据竞争问题。在生产环境中，可以使用正常的构建方式来编译程序。

## 反射

### reflect.Type与reflect.value

- reflect.Kind获取元素

```go
func ResolveSearchQuery(ins interface{}, db *gorm.DB) *gorm.DB {
	st := reflect.TypeOf(ins).Elem()//获取指针指向的类型
	
	nums := st.NumField() //struct 获取成员变量的数量
	for i := 0; i < nums; i++ {

		fd := st.Field(i) //指定索引的成员
		key := fd.Tag.Get("json") //获取成员标签
		val := reflect.ValueOf(ins).Elem().FieldByName(fd.Name) //根据成员的key获取reflect.value

        //isNil只能判断指针类型 isZero可以判断是否为零值 
		if !val.IsZero() { //判断reflect.val 是否为nil值
			db = db.Where(fmt.Sprintf("%s = ?",key) ,val.Interface()) //interface 获取reflectVal的原始值
			fmt.Println(st.Field(i).Name)
		}
	}
	return db
}
```



## WASM

windows 可以使用`scoop`下载需要的包

1. 使用`tinygo`


```shell
tinygo build -o wasm.wasm -target wasm ./main.go 
```

`wasm_exec.js`需要使用`C:\Program Files\tinygo\targets`下的，不然会报错

- `go`中给`js`暴露方法可以添加一个注释 

```go
//go 中暴露方法
//export sum
func sum(a int, b int) int {
	return a + b
}
```

```js
//js 中使用
const go = new Go();
  WebAssembly.instantiateStreaming(fetch("../test.wasm"), go.importObject).then(function (result) {
    go.run(result.instance);
    const addRes = result.instance.exports.sum;
    console.log(addRes(20, 10));
  });
```

2. 使用go build

> 需要配置build env 
>
> 可以直接在环境变量目录内的env GOOS=js GOARCH=wasm

- 暴露方法需要使用`syscall/js`等库

```shell
go build -o test.wasm main.go
# go bash 使用
GOOS=js GOARCH=wasm go build -o test.wasm main.go
```

前端需要引入`wasm_exec.js`

```shell
cp "$(go env GOROOT)/misc/wasm/wasm_exec.js"
```
