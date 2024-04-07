# Rust笔记

- 在rust中 变量是默认不可变的
- String 的trim 方法会去除 换行符
- Rust 是一门基于表达式（expression-based）的语言

## 函数

#### 语句和表达式

​	函数体由一系列的`语句`和一个`可选的结尾表达式`构成

- **语句**（*Statements*）是执行一些操作但不返回值的指令，函数定义也是语句，语句不返回值。因此，不能把 `let` 语句赋值给另一个变量

```rust
fn main() {
    let x = (let y = 6); //error
}
```

- **表达式**（*Expressions*）计算并产生一个值,表达式的结尾没有分号

#### if 表达式

- `if` 的每个分支的可能的返回值都必须是相同类型，并且可以赋值给变量

```rust
let value = if condition {5}else{6};
```

#### 循环`loop`

- 使用`break`中断
- 区分不同的`loop`可以添加一个标签`'custom_label`

#### 使用`for`遍历集合

- `Range`

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{number}!");
    }
    println!("LIFTOFF!!!");
}
```

## 认识所有权

### 所有权

- 所有权让`rust`无需垃圾回收（garbage collector）即可保障内存安全
- `Rust`没有`GC`,Rust 采取了一个不同的策略：内存在拥有它的变量离开作用域后就被自动释放，当变量离开作用域，Rust 为我们调用一个特殊的函数。这个函数叫做 [`drop`](https://doc.rust-lang.org/std/ops/trait.Drop.html#tymethod.drop)，在这里 `String` 的作者可以放置释放内存的代码。Rust 在结尾的 `}` 处自动调用 `drop`

- 当一个堆中的变量赋值给另一个变量时，两个变量的`指针`将指向`相同的堆中的数据`,当离开作用域时，由于变量离开作用域时自动释放，会释放两次，`rust`为了避免这种情况，`rust`会认为第一次声明的变量无效，然后释放被赋值的变量，称之为`移动(move)`

```rust
 let s1 = String::from("hello");
    let s2 = s1;

    println!("{}, world!", s1); //error borrow of moved value: `s1`
```

```rust
fn main() {
    let s = String::from("hello");  // s 进入作用域

    takes_ownership(s);             // s 的值移动到函数里 ...
                                    // ... 所以到这里不再有效

    let x = 5;                      // x 进入作用域

    makes_copy(x);                  // x 应该移动函数里，
                                    // 但 i32 是 Copy 的，
                                    // 所以在后面可继续使用 x

} // 这里，x 先移出了作用域，然后是 s。但因为 s 的值已被移走，
  // 没有特殊之处

fn takes_ownership(some_string: String) { // some_string 进入作用域
    println!("{}", some_string);
} // 这里，some_string 移出作用域并调用 `drop` 方法。
  // 占用的内存被释放

fn makes_copy(some_integer: i32) { // some_integer 进入作用域
    println!("{}", some_integer);
} // 这里，some_integer 移出作用域。没有特殊之处
```

#### 栈与堆

- 堆：在`编译时大小未知`或`大小可能变化`的数据，要改为存储在`堆`上，堆是缺乏组织的：当向堆放入数据时，你要请求一定大小的空间。内存分配器（memory allocator）在堆的某处找到一块足够大的空位，把它标记为已使用，并返回一个表示该位置地址的 **指针**（*pointer*）。这个过程称作 **在堆上分配内存**（*allocating on the heap*），有时简称为 “分配”（allocating）。因为指向放入堆中数据的指针是已知的并且大小是固定的，你可以将该指针存储在栈上，不过当需要实际数据时，必须访问指针
- 栈：后进先出，叠盘子，栈中的所有数据都必须占用已知且固定的大小，入栈出栈
- 入栈比在堆上分配内存要快
- 访问栈上的数据比访问堆上的数据块，因为堆必须通过指针来访问，现代处理器在内存中跳转越少就越快（缓存）
- 如果确实需要`clone`堆上的数据，可以使用`clone方法`,`let s2 = s1.clone();`
- ==**如果一个类型实现了 `Copy` trait，那么一个旧的变量在将其赋值给其他变量后仍然可用**==。如下是一些 `Copy` 的类型：
  - 所有整数类型，比如 `u32`。
  - 布尔类型，`bool`，它的值是 `true` 和 `false`。
  - 所有浮点数类型，比如 `f64`。
  - 字符类型，`char`。
  - 元组，当且仅当其包含的类型也都实现 `Copy` 的时候。比如，`(i32, i32)` 实现了 `Copy`，但 `(i32, String)` 就没有。

#### `String`类型

- 字面量：`let a = "string"`,字面量字符串是不可变的
- `String`: `let s = String::from("hello");`,可以使用`push_str`方法追加内容,对于 `String` 类型，为了支持一个可变，可增长的文本片段，需要在堆上分配一块在编译时未知大小的内存来存放内容

### 引用与借用

- 在任意给定时间，**要么** 只能有一个可变引用，**要么** 只能有多个不可变引用。
- 引用必须总是有效的。

#### 引用

- `&`符号实现一个变量的引用,解符号`*`
- 引用的值不支持修改

#### 可变引用

- 只能创建一个可变引用，这一限制可以避免在编译时的数据竞争`（data race）`
- 可以使用大括号`{}`创建一个新的作用域创建新的可变引用
- 同时使用可变引用与引用时也遵信这一规则

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}

//这种会报错 只能创建一个可变引用
let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;

    println!("{}, {}", r1, r2);

```

#### 悬垂引用

很容易通过释放内存时保留指向它的指针而错误地生成一个 **悬垂指针**（*dangling pointer*），所谓悬垂指针是其指向的内存可能已经被分配给其它持有者

### slice类型

- `str`类型为`string`类型的切片类型

```rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();
    for (index,&item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[..index];
        }
    }
    &s[..]
}
```

## 使用结构体处理数据

创建与go相似

#### 普通结构体

```rust
struct User {
    user_name:String,
    password:String,
    age:i32
}
let mut user1 = build_user(String::from("zhangsan"));
```

可以使用`..`来融合结构体

```rust
//此方法创建user2会使user1种的字段转移（move）到user2当中 此时user1 不可以再使用了 除了没有用到的字段
fn main() {
    // --snip--

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

#### 元组结构体

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

#### 类单元结构体

为这个类型实现某种行为

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

#### 打印结构体

```rust
#[derive(Debug)] //需要在结构体显式选择外部属性 #[derive(Debug)]
struct Rect {
    width: u32,
    height: u32,
}
fn main() {

    let rect = Rect {
        width: 30,
        height: 50
    };
    println!("{:#?}", rect); //使用 {:#?} 比 {:?} 打印的结构更清晰一点
   
}
//或者使用deb宏
 dbg!(&rect1);
dbg!(30 * 20) //30 * scale = 60
```

#### 给struct定义方法

- 我们可以选择将方法的名称与结构中的一个字段相同
- `Rust`有一个叫 **自动引用和解引用**（*automatic referencing and dereferencing*）的功能,这些代码是等价的：

```rust
p1.distance(&p2);
(&p1).distance(&p2);
```

impl中定义的方法称为关联函数

```rust
#[derive(Debug)]
struct Rect {
    width: u32,
    height: u32,
}
impl Rect {
    fn area(&self) -> u32 { //&self 实际上是 self: &Self的缩写
        self.width * self.height //使用self访问结构体本身
    }
}
fn main() {
    let rect = Rect {
        width: dbg!(30 * 20),
        height: 50
    };
    dbg!(rect.area());
}

//要想操作结构体本身需要参数改为 &mut self，
impl Rect {
    fn area(&mut self) -> u32 {
        self.width = 80;
        self.width * self.height
    }
}
fn main() {

    let mut rect = Rect {
        width: dbg!(30 * 20),
        height: 50
    };
    dbg!(rect.area());
   
}
```

## 枚举和模式匹配

#### 普通枚举

枚举的关联值

```rust
enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);

    let loopback = IpAddr::V6(String::from("::1"));
```

枚举同样可以像`struct`那样使用`impl`创建一个方法

```rust
  impl Message {
        fn call(&self) { // 这里的self指向新创建的m
            // 在这里定义方法体
        }
    }

    let m = Message::Write(String::from("hello"));
    m.call();
```

#### Option枚举

- 使用`Option<&T>.copied`方法可以获取一个`Option<T>`
- 可以使用`unwrap`解包`Option`的值，使用`unwrap_or`在值为`None`的时候设置一个默认值

`Option` 类型应用广泛因为它编码了一个非常普遍的场景，即一个值要么有值要么没值

被定义于标准库当中，可以直接访问其成员`Some`,`None`

```rust
enum Option<T> {
    None,
    Some(T),
}
//使用
let val:Option<i8> = Some(20);
    let result = match val {
        Some(x) => {
            Some(x) 
        },
        None => {
            None
        },
    };
    println!("{:?}",result) // 20
```

#### match控制流结构

```rust
//在这个例子中 每个分支相关联的代码是一个表达式，而表达式的结果值将作为整个 match 表达式的返回值。
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

##### 绑定值的模式

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state)=>{
            print!("State quarter from {:?}!\n", state);
            match state {
                UsState::Alabama => 26,
                UsState::Alaska =>27
            }
        },
    }
}
```

##### 通配模式和_占位符

```rust
fn value_in_cents(num: u8) -> u8 {
    match num {
        1=>20,
        2=>40,
        other=>other*5
        //_=>some_fun() 其他值不做操作的话可以使用_占位符
    }
}
```

##### `if let`语法糖

```rust
enum NumE {
    T,
    D,
    F
}
let val = NumE::D;
    if let NumE::D = val {
        println!("{:?}",val)
    }else{
        println!("nothing")
    }
```

## Rust的模块系统（包，Crate，模块管理）

### 包和Crate

`Crate` :是 `Rust` 在`编译时`最小的代码单位,有两种形式 `二进制`和`库`

`Crate根节点`:当编译一个 crate, 编译器首先在 crate 根文件（通常，对于一个`库` crate 而言是*`src/lib.rs`*，对于一个二进制 crate 而言是*`src/main.rs`*

*`包`*（*`package`*）:是提供一系列功能的一个或者多个 crate。一个包会包含一个 *Cargo.toml* 文件，阐述如何去构建这些 crate。Cargo 就是一个包含构建你代码的二进制项的包。Cargo 也包含这些二进制项所依赖的库。其他项目也能用 Cargo 库来实现与 Cargo 命令行程序一样的逻辑。

- `包`中可以包含至多一个库 crate(library crate)
- `包`中可以包含任意多个二进制` crate(binary crate)`，但是必须至少包含一个 `crate（无论是库的还是二进制的）`

- **从 crate 根节点开始**: 当编译一个 crate, 编译器首先在 crate 根文件（通常，对于一个库 crate 而言是*src/lib.rs*，对于一个二进制 crate 而言是*src/main.rs*）中寻找需要被编译的代码。

- 声明模块

  : 在 crate 根文件中，你可以声明一个新模块；比如，你用

  ```
  mod garden
  ```

  声明了一个叫做

  ```
  garden
  ```

  的模块。编译器会在下列路径中寻找模块代码：

  - 内联，在大括号中，当`mod garden`后方不是一个分号而是一个大括号
  - 在文件 *src/garden.rs*
  - 在文件 *src/garden/mod.rs*

### 定义模块

生成lib的命令行

```powershell
cargo new --lib testModle
```

- 声明子模块

  : 在除了 crate 根节点以外的其他文件中，你可以定义子模块。比如，你可能在

  src/garden.rs

  中定义了

  ```
  mod vegetables;
  ```

  。编译器会在以父模块命名的目录中寻找子模块代码：

  - 内联，在大括号中，当`mod vegetables`后方不是一个分号而是一个大括号
  - 在文件 *src/garden/vegetables.rs*
  - 在文件 *src/garden/vegetables/mod.rs*

- **模块中的代码路径**: 一旦一个模块是你 crate 的一部分，你可以在隐私规则允许的前提下，从同一个 crate 内的任意地方，通过代码路径引用该模块的代码。举例而言，一个 garden vegetables 模块下的`Asparagus`类型可以在`crate::garden::vegetables::Asparagus`被找到。

- **私有 vs 公用**: 一个模块里的代码默认对其父模块私有。为了使一个模块公用，应当在声明时使用`pub mod`替代`mod`。为了使一个公用模块内部的成员公用，应当在声明前使用`pub`。

- **`use` 关键字**: 在一个作用域内，`use`关键字创建了一个成员的快捷方式，用来减少长路径的重复。在任何可以引用`crate::garden::vegetables::Asparagus`的作用域，你可以通过 `use crate::garden::vegetables::Asparagus;`创建一个快捷方式，然后你就可以在作用域中只写`Asparagus`来使用该类型。

### 引用模块项目的路径

路径有两种形式：

- **绝对路径**（*absolute path*）是以 crate 根（root）开头的全路径；对于外部 crate 的代码，是以 crate 名开头的绝对路径，对于当前 crate 的代码，则以字面值 `crate` 开头。
- **相对路径**（*relative path*）从当前模块开始，以 `self`、`super` 或当前模块的标识符开头。
- 绝对路径和相对路径都后跟一个或多个由双冒号（`::`）分割的标识符。
- 可以使用`use`关键字在导入的时候简化路径导入
- 使用 `use` 引入结构体、枚举和其他项时，习惯是指定它们的完整路径

使用 `super` 允许我们引用父模块中的已知项，

### 结构体及枚举

- 结构体可以定义成员是否私有
- 将枚举标记为`pub`则其所有成员都为公有

### 可以使用`as`关键字提供新的名称

使用`pub`关键字结合`use`可以将函数导出为新抿成

```rust
pub use crate::front_of_house::hosting::add_to_waitlist as add_list
```

### 嵌套路径消除大量行

```rust
use std::cmp::Ordering;
use std::io;
//等同于
use std::{cmp::Ordering, io};

//self表示引入包自己 io
use std::io::{self, Write};

//将所有公有项引入
use std::collections::*;
```

## 常见集合

> 集合存放在堆当中 是不定大小的

### Vec<T>(vector)

- vector 只能储存相同类型的值
- 在访问vectior的引用时不允许修改整体，`push`进一个元素可能指向的内存发生了变化，从而让引用指向一块已经废弃的内存
- Rust 在编译时就必须准确的知道 vector 中类型的原因在于它需要知道储存每个元素到底需要多少内存。第二个好处是可以准确的知道这个 vector 中允许什么类型
- 离开作用域时所有成员也会释放

```rust
//创建
let v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];

//获取元素可以使用索引或者 get
let third: &i32 = &v[2];
let third: Option<&i32> = v.get(2);

 //可以使用枚举来存储多种数据类型
enum SpreadsheetCell {
        Int(i32),
        Float(f64),
        Text(String),
    }
let row: Vec<SpreadsheetCell> = vec![
        SpreadsheetCell::Int(20)
    ];
```

### String

- `String` 和 字符串 slices 都是 UTF-8 编码的。
- `&str` 是一个字符串切片，它只是指向一个字符串，而不拥有这个字符串。`String` 是一个字符串堆分配对象，它拥有自己的字符串内存空间
-  `&String` 可以被 **强转**（*coerced*）成 `&str`。`Deref 强制转换（*deref coercion*）`
- 格式化字符串可以使用`format!`宏,返回一个`String`
- `Rust`当中字符串的`len`是`utf-8`定义的字节数
- `String`不能通过索引访问，访问字符串slice不当可能会发生`panic`

#### 遍历字符串

```rust
//遍历字符
for c in "Зд".chars() {
    println!("{c}");
}
//遍历字节
for b in "Зд".bytes() {
    println!("{b}");
}
```

### HashMap

```rust
//使用from创建hashMap
let tuples = vec![
        ("apple", 1),
        ("banana", 2),
        ("cherry", 3),
    ];
let hs_map =  HashMap::from(tuples)
//在没有对应值的时候插入
scores.entry(String::from("Yellow")).or_insert(50);
```

