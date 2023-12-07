# GoLang

​	Go是一门编译型语言，Go 语言的工具链将源代码及其依赖转换成计算机的机器指令（译注：静 态编译）

​	`make` 函数用于`动态分配内存空间`，它会根据提供的参数自动计算所需的空间大小，并返回一个指向该内存区域的指针。使用 `make` 分配内存空间可以确保内存空间被正确初始化，避免因未初始化的内存空间导致的数据竞争或其他问题。

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

`fmt.Printf`

### 数据类型

`var`变量声明，声明时直接初始化,如果没有显式初始化则被隐式赋予`零值(zero value)`s数值类型是0，字符串类型是“”

`nil`:相当于其他语言的null

`slice`切片，可以使用`s[m:n]`获取子序列，省略m/n 会默认传入`0`或`len[s]`

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

### 测试脚本

- 文件名：module_test.go
- 相关包：regexp、testing

### 杂项

查看go安装目录`go list -f '{{.Target}}' `