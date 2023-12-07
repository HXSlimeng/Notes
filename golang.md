# GoLang

​	Go是一门编译型语言，Go 语言的工具链将源代码及其依赖转换成计算机的机器指令（译注：静 态编译）

​	Go语言最有意思并且最新奇的特性就是对并发编程的支持

​	原生支持Unicode

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

`fmt`：`printf`是指`print format`，`println` 是指 `print line`

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

  

### 测试脚本

- 文件名：module_test.go
- 相关包：regexp、testing

### 杂项

查看go安装目录`go list -f '{{.Target}}' `

`go run src/**.go &`在末尾加入一个`&`可以让程序简单地跑在后台

`go doc`godoc这个工具可以让你直接在本地命令行阅读标准库的文档 example `go doc http.ListenAndServe`

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

