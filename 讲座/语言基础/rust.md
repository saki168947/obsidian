 # **<font color="#4f81bd"> "rust": 编程中的原神</font>**
 ## 参考文献（
>  [Rust 为什么被称作编程界原神？ - 知乎](https://www.zhihu.com/question/649312963?write)





![[Pasted image 20260125005826.png]]
# 介绍
**Rust** **和 golang**(go语言) **TypeScript**（TS)  **Kotlin**  同属于现代高性能语言
## 与其他语言对比
1.  Rust 学习难度大，代码特别复杂 ，不如go好上手 ，很多项目不需要使用到rust
2. Rust既没有创造出新赛道,也做不到同赛道上超越或替代已有语言,对于c++远不如TypeScript之于JavaScript
3. 但这仍然不妨碍它成为近年最受欢迎的语言（撇开某些信徒）
4. （ 我说rust是最好的语言有没有懂的）
(绝对不要用rust尝试链表等数据结构，这太难了，长官)
## 我个人认为Rust的优点
1. FP风格  函数式编程，容易理解
2. 变量一旦赋值不可以改变
3. 用组合代替命令：像乐高积木一样把简单函数拼成复杂功能，而不是写一堆"先做A再做B"的指令
4. 报错直接在缓冲区 or 终端说明问题在哪里 （神中神）
5. 运行速度快


# 环境配置

1. 先安装MSCV(Microsoft c++ 生成工具)[适用于 Windows、Mac 和 Linux 的 Visual Studio 和 VS Code 下载](https://visualstudio.microsoft.com/zh-hans/downloads/)
![[Pasted image 20260201131853.png]]
2.rust官网安装界面[安装 Rust - Rust 程序设计语言](https://rust-lang.org/zh-CN/tools/install/)
![[Pasted image 20260201131534.png]]

打开文件，会自动检测是否安装msvc
![[Pasted image 20260201132358.png]]
这是没安装的

![[Pasted image 20260201133613.png]]

这是安装了的
默认安装（点1）就可以开始下载组件了
![[Pasted image 20260201133820.png]]
安装完毕！
3.添加path环境变量
![[Pasted image 20260201134019.png]]

查看安装效果
![[Pasted image 20260201134219.png]]

4.rust开发
在vscode中用rust编译器
![[Pasted image 20260201135132.png]]

在命令行中打开一个文件夹
![[Pasted image 20260201135222.png]]
```rust
cargo new 创建项目
```
![[Pasted image 20260201135321.png]]

![[Pasted image 20260201135505.png]]

cargo 的项目类型一般是 src里面放源码，cargo.toml里面进行包管理
cargo.lock是用来锁定cargo.toml里面每个包的版本


# 所有权系统 (Ownership)
- rust 中每一个值都存在一个**owner（所有者）**的变量

- 值在任何一个时刻都有且仅有一个所有者

- 当所有者（变量）离开作用域，这个值将被**丢弃（Drop/Free）**

在GC语言（PYTHON,Java)中，如果你写b=a,就相当于复制了一个一样的值，两个值都能使用，互不影响

在rust中，如果你写`let b = a;`，这叫**“转让”**（Move）。如果这个时候你去`println!({},a)`,就会直接报错

## 为什么这么做
核心目标只有一个：**在不依赖垃圾回收（GC）的前提下，彻底解决内存安全和并发安全问题**。

我们可以对比其他语言的痛点，就能理解 Rust 的 “绝” 其实是 “对症下药”：

#### 1. 解决 C/C++ 的 “自由但危险” 问题

C/C++ 让程序员手动管理内存（malloc/free、new/delete），灵活性极高，但代价是：

- 忘记释放内存 → 内存泄漏；
- 提前释放内存 → 悬垂指针（你的代码报错就是这类问题的 Rust 提前拦截）；
- 多线程同时修改数据 → 数据竞争（崩溃或诡异的 bug）。

这些问题的特点是：**编译时发现不了，运行时随机出现，调试难度极大**（比如上线后偶尔崩溃，根本复现不了）。

Rust 的做法是：**用所有权规则把这些问题变成编译错误**—— 你写代码时就必须解决，否则程序根本跑不起来。

#### 2. 解决 GC 语言（Java/Python/Go）的 “安全但有代价” 问题

Java/Python 用垃圾回收自动管理内存，解决了内存安全，但代价是：

- GC 运行时会 “暂停” 程序（Stop The World），无法用于对延迟敏感的场景（比如嵌入式、实时系统、内核开发）；
- 程序员无法精细控制内存，某些高性能场景（比如游戏引擎、数据库）会有性能损耗；
- 即使有 GC，多线程的数据竞争问题依然存在（比如 Java 还要靠 synchronized 手动加锁）。

---
# 借用与引用 (Borrowing & References)
在 Rust 里，**引用**就是**指针**（地址），而**借用**就是**创建这个指针的过程**。

## 引用
1. 不可变引用 (`&T`) —— “只读权限”
	如果你只有一个数据，要给另外两个地址，你不需要给出数据的所有权，而可以创建引用&，两个地址只能存放，不能修改
```rust
	fn main(){

    let s = String::from("Hello, world!");

    let s1 = &s;

    let s2 = &s;

    println!("{}, {}", s1, s2);

}
```

	你会打印出两个一样的hello world，但不能修改

2. 可变引用 (`&mut T`) —— “独占编辑权限”

当我们的数据是变量，就要使用可以可变地址`&mut`,这个时候我们的引用可以修改
```rust
fn main(){  
	let mut s = String::from("ciallo");

    let r3 = &mut s;

    //r3.push_str(" ～(∠・ω< )⌒⭐");

    //如果不加入上面，会println  ciallo，用上面的会println  ciallo ～(∠・ω< )⌒⭐

    println!("{}", r3);

}
```

## 借用
借用指的是创建引用的过程/动作

- rust 的借用是一个有借有还的过程，类似于一种检查机构

1.权限管理

简单来说就是，让不可变引用不被修改或者所有权转让，让可变引用不被别人访问（排他性[^2]）

2.有效性保障

**借用** 的工作是：**确保在引用还活着的时候，内存绝对不能被释放**

---

# 生命周期 (Lifetimes)
生命周期的作用只有一个：让编译器确保“指针活得绝对不能比数据长”。


反面例子：

```rust
fn main(){ 
 let r;//引用r出生了

    {

        let x = 5;//数据x出生了

        r = &x;//r指向x

    }//x死了（出作用域了）

    print!("r: {}", r);//报错，数据死了，引用还活着

}
```

## 生命周期自动推导
绝大多数时候，rust编译器可以看出谁活得久，这叫做**生命周期自动推导**

但当函数存在多个输入引用的时候，就会出问题，直接给你报错

例如：

比较两个字符串长度的函数

```rust
fn longest(x: &str, y: &str) -> &str {

    if x.len() > y.len() {

        x

    } else {

        y

    }

}

fn main() {

    let string1 = String::from("abcd");

    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);

    println!("The longest string is {}", result);

}
```

## 'a  ———生命周期的标注
它不是在改变变量的寿命，它只是在**描述关系**。
```rust
fn longest<'a >(x: &'a str, y: &'a str) -> &'a str {

    if x.len() > y.len() {

        x

    } else {

        y

    }

}

  
  

fn main() {

    let string1 = String::from("abcd");

    let string2 = "xyz";

  

    let result = longest(string1.as_str(), string2);

    println!("The longest string is {}", result);

}
```

修改后的函数'a，x , y 还有 返回值 共用一个生命周期，这里面周期最短的，会覆盖所有的生命周期

---

# 速通基础语法
## 编写第一个 hello world
```rust
fn main() {
    println!("Hello, world!");
}
```
- **使用 `fn` 关键字定义函数，指定参数和返回类型。**
- **`println!(n)` 直接打印变量` n`的值，不添加任何格式化。**

**为什么编译后会生成 `.pdb` 和 `.exe` 文件？**
    - **.exe (Executable)**: Windows 平台的可执行文件。这是编译器将你的 Rust 源代码翻译成计算机能读懂的机器码及其封装结果，双击即可运行。
    - **.pdb (Program Database)**: 程序数据库文件，存储了调试符号信息。
    - **作用**: 它将编译后的机器码映射回你的源代码行号和变量名。如果程序崩溃或你需要调试（断点、单步执行），调试器需要读取 `.pdb` 文件才能告诉你错误发生在源代码的哪一行，而不是一堆十六进制代码。

## 语法

首先，让我们快速浏览一下 Rust 的语法。这些内容可能很熟悉，但与其他语言相比，Rust 有一些小特点。

注：以上观点是根据 Will Crichton 的 [CS 242 Rust 实验讲义](http://cs242.stanford.edu/f19/labs/rust)扩展的，还有CS 110L[CS 110L：系统编程中的安全性 --- CS 110L: Safety in Systems Programming](https://reberhardt.com/cs110l/spring-2020/assignments/week-1-exercises/)

## 变量和常量
Rust 中的数值类型包括 `i8`、`i16`、`i32` 和 `i64`（这些类型都存储带符号的正数或负数），以及 `u8`、`u16`、`u32` 和 `U64`（存储无符号——严格非负数）。

要声明变量，我们使用 `let` 关键字并指定变量类型：

```Rust
let n: i32 = 1;
```
---
  Rust 不要求你声明每个变量的类型，而是允许你省略变量的类型，因为编译器能确定该类型
  
```Rust
let n = 1;
```

---
  Rust 中的变量默认是常_量。_ 此举旨在减少错误;如果你修改了一个你不打算修改的变量（比如没有明确标记为可变），编译器会给你报错。添加 `mut` 使变量可变。

```Rust
let mut n = 0;
n = n + 1;
```
---


## 控制结构
-  **顺序结构：略**

- **选择结构：**

	**1.基础选择结构：`if-else`**
		![[Pasted image 20260125020340.png]]
	---
		
	**2.模式匹配：`match`**
			**类似于C语言`switch`语句，但功能远超 `switch`，它支持模式匹配、解构、绑定变量等高级特性，是 Rust 中的核心语法之一。**
		**示例 1：基础数值匹配（替代 `switch`）**
	![[Pasted image 20260125020711.png]]
	**示例2 :解构元组(枚举，结构体)**
	![[Pasted image 20260125020954.png]]
---
## 循环结构
1. **条件循环(while 循环)**
	**语法：`while 布尔条件 { 循环体代码 }`，条件无需加圆括号 `()`，必须是严格的 `bool` 类型（无隐式类型转换）**

```Rust
while i < 20 {
    i += 1;
}
```
---
2. **无限循环(loop循环)**
	语法：`loop { 循环体代码 }`，无需条件判断，直接进入循环

```Rust
let mut i = 0;
loop {
    i += 1;
    if i == 10 { break; }
}
```

---
3. **遍历循环(for循环)**
	**语法：`for 变量 in 可迭代对象 { 循环体代码 }`**

```rust
for i in 1..5 
	println!("当前数字：{}", i); 
```

---

## 最后，Rust 函数的声明如下：

```Rust
fn sum(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {

}
```
[^1]

[^1]: **rust函数中的分号**
	
	    - 普通语句必须加分号：
	
	      - 例如：`let x = 5;`
	
	    - 返回表达式不加分号：
	
	      - 例如：`x + 1` (表示返回 `x + 1` 的值)
	
	    - return语句要加分号


---

# 关于嵌入式
- **rust操作底层或者想做框架很麻烦，并且编译出来的东西很大**
- rust 在嵌入式领域的最大卖点是：**在不牺牲性能（零运行时开销）的前提下，获得极高的安全性和现代化的开发体验。**


# 关于WASM(WebAssembly)

**”如果 2008 年就有 WASM+WASI，我们就没必要发明 Docker 了“**

## 介绍
  - **是一种在浏览器中取代Javascript来进行计算密集型应用的方式。**
  - **WASM二进制文件可以实现“编译一次，就可到处运行”，而与底层平台无关。(很容易联想到docker)**
---
## 与Rust
**Rust 是目前编写 WebAssembly 最完美、最成熟的语言选择，没有之一**

**上面说了，因为WASM是二进制文件，而rust没有GC(垃圾回收)环节，导致编译出的 WASM 文件极小（经过优化后可以是几十 KB），且运行时性能极其稳定。**

**而且，在浏览器这样一个沙盒环境中，Rust 的内存安全性防止了常见的崩溃和漏洞，这比使用 C/C++ 编译 WASM 要安全得多**

**再甚至，rust官方存在这一整套rust的前端（如：Yew ,Leptos,Dioxus)**


**TypeScript (TS) + Rust 已经被公认为是目前现代编程界的 主流工具**


[^2]: 当一个变量被借出可变引用（&mut）时，在这个可变引用的生命周期内，原变量和任何其他引用（不管可变还是不可变）都无法被访问
