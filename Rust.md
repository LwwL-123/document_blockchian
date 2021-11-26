# Rust

​		Rust 程序设计语言能帮助你编写更快、更可靠的软件。在编程语言设计中，高层工程学和底层控制往往不能兼得；Rust 则试图挑战这一矛盾。通过权衡强大的技术能力与优秀的开发体验，Rust 允许你控制底层细节（比如内存使用），并免受以往进行此类控制所经受的所有烦恼。



# 1.编译运行

- 运行Rust之前必须先编译：

```rust 
rustc main.rs
```

- 编译成功后，会生成一个二进制文件
- Rust是一个ahead-of-time的语言
  - 可以先编译，然后将可执行程序交给别人运行（无需安装rust）



# 2.Cargo

是一个Rust构建系统和包管理工具

- 构建代码，下载依赖的库，构建这些库
- cargo.toml

```toml
[package]   				
name = "Rust_Study"						//项目名
version = "0.1.0"							//项目版本
authors = ["lzw <657434763@qq.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies] //依赖
```

- cargo.lock：第一次运行cargo build会在顶层目录下生成
  - 该文件负责追踪项目依赖的精确版本
  - 不需要手动修改该文件





# 3.猜数字游戏

```rust
use std;
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("猜数");
    let screat_numeber = rand::thread_rng().gen_range(1, 100);
    println!("神秘数字是：{}", screat_numeber);

    loop {
        println!("请输入你猜的数：");
        //rust里面默认不可变constant，要加mut使变量可变
        let mut guess = String::new();

        io::stdin().read_line(&mut guess).expect("无法读取");
        let guess: u32 = match guess.trim().parse().expect("错误") {
            Ok(num) => num,
            Err(_) => continue,
        };//string转int

        match guess.cmp(&screat_numeber) {
            Ordering::Less => { println!("小了") }
            Ordering::Equal => {
                println!("相等");
                break;
            }
            Ordering::Greater => { println!("大了"); }
        }
    }
}

```





# 4.变量和可变性

- 声明变量用let关键字
- 默认情况下，变量不可变（immutable）
- 在前面加上mut，使变量可变



## 4.1 常量（constant）

- 不可以使用mut，常量永远不可变
- 用const关键字
- `命名规范`：Rust里面常量全大写字母，每个单词使用下划线分开
  - MAX_POINT



## 4.2 Shadowing 隐藏

- 在Rust中可以使用相同名字声明新的变量，新的变量就会Shadowing隐藏之前的同名变量



- shadow和把变量标记为mut是不一样的
  - 如果不使用let关键字，那么重新给非mut的变量赋值会导致编译错误
  - 而使用let声明的同名新变量，也是不可变的
  - 而使用let声明的新变量，类型可以与之前不同

```rust
fn main() {
    let x = 5;
    println!("数字是{}", x);

    let x = 10;
    println!("数字是{}", x);
  
  	//错误
  	//X=X+10;
}
```



# 5. 数据类型

- 标量和复合类型
- Rust是静态编程语言，在编译时必须知道所有变量的类型
  - 基于使用的值，编译器通常能推断他的具体类型
  - 但如果可能的类型比较多，就必须添加类型的标注（String=>int）



## 5.1 标量类型

### 5.1.1 整数类型

- 无小数
- 无符号u
  - 范围：0~2^n^-1
- 有符号i
  - 范围：-(2^n^-1)~2^n-1^-1



整数溢出：

- 例如u8的范围是0~255，如果把一个u8设置为256，那么：
  - 调试模式下编译，会检查整数溢出，会导致程序panic（恐慌）
  - 发布模式下 编译，程序不会panic，会执行环绕操作，如256变成0，257变成1

### 5.1.2 浮点类型

- f32，32位，单精度
- f64，64位，双精度（默认）



### 5.1.3 布尔类型

- 一个字节大小
- bool



### 5.1.4 字符类型

- char类型，单个字符
- 单引号
- 4字节



## 5.2 复合类型

- 复合类型可以将多个值放在一个类型里
- 元祖（Tuple），数组

### 5.2.1 Tuple元祖

```rust
fn main() {
    let tup:(i32,f64,u32) = (500,6.4,1);
    let (x,y,z) = tup;	
    println!("{},{},{}",x,y,z);
    println!("{},{},{}",tup.0,tup.1,tup.2);
}
```



### 5.2.2 数组

- 如果你想让你的数据存放在stack上而不是heap上，或者想保证有固定数量的元素，就可以使用数组

- 没有vector灵活，vector长度可变

- ```rust
  let a:[u32;5] = [1,2,3,4,5];
  ```

- ```rust
  let a:[3,5]; //相当于let a = [3,3,3,3,3];
  ```

- 如果数组越界

  - 编译会通过
  - 运行时会panic
    - Rust不允许其继续访问相应地址的内存



# 6. 函数

- fn关键字
- 针对变量名和函数，Rust采用 snack case命名规范：
  - 所有字母都是小写，单词间用下划线
- 返回值，默认最后一个值作为返回值

```rust
fn aaa(x:u32) -> u32 {
  //没有分号，有分号就变成了语句，就没有返回值了
    x+5
}

fn main() {
    let y =aaa(10);
    println!("{}",y);
}
```



# 7. 控制流

## 7.1 if

- if后必须是bool类型
- 如果使用多于一个if-else最好使用match

```rust
fn aaa(x:u32) -> u32 {
    x+5
}

fn main() {
    let y =aaa(10);
  
    if y<20 {
        println!("{}",y);
    } else {
        println!("{}",y+10);

    }
}
```

## 7.2 循环

- loop
- while
- for循环

```rust
fn main() {
    let a = [1,2,3,4,5];
    for element in a.iter() {
        println!("{}",element);
    }
}
```

Range(1..4) 从1-3

```
fn main() {
    for num in (1..4).rev() {
        println!("{}",num);
    }
}
```





# 8. *所有权

所有权是Rust最独特的特性，他让Rust无需GC就可以保证内存安全

## 8.1 什么是所有权

- Rust的核心特性就是所有权
- 所有程序在运行时都比要要管理他们使用计算机内存的方式
  - Java-GC
  - C-显示分配和释放
- Rust采用了第三种方式：
  - 内存是通过一个所有权系统来管理的，其中包含一组编译器在编译时检查的规则
  - 当程序运行时，所有权特性不会减慢程序的运行速度

## 8.2 Stack 和 Heap

`差别`

- 存储在Stack上的数据必须拥有已知的固定的大小
- 编译时大小未知的数据或运行时大小可能发生改变的数据必须存放在heap上



- Heap的内存组织性稍差一些
  - 当把数据存入heap时，会请求一定数量的空间
  - 操作系统在heap里找到一块足够大的空间，标记为在用，并返回一个指针，也就是这个空间的地址
  - 这个过程叫分配
- 因为指针的大小是固定的，所以可以把指针放在stack上
  - 如果想使用实际数据，必须用指针来定位
- 把数据压到stack上要比在heap上分配快很多
  - 因为OS不需要寻找来存储的空间，这个位置永远在stack的顶端
- 在heap上分配空间需要做更多的工作
  - OS需要找到一个足够大的空间，并做好记录，方便下次分配

`效率`

- 访问heap要比stack中的数据慢，因为需要通过指针才能找到heap中的数据



## 8.3 所有权存在的原因

- 所有权解决的问题：
  - 跟踪代码的哪些部分正在使用heap的数据
  - 最小化heap上的重复数据量
  - 清理heap上未使用的数据，以避免空间不足
- 一旦懂了所有权，就不需要经常去想stack和heap了



## 8.4 所有权规则

- 每个值都有一个变量，这个变量是改值的所有者
- 每个值同时只能有一个所有者
- 当所有者超出scope（作用域）时，改值会被删除



## 8.5 String和str

### 8.5.1 String创建

```rust
fn main() {
    let mut s = String::from("hello");
    s.push_str(",lzw");
    println!("{}",s);
}
```

### 8.5.2 内存和分配

- 字符串字面值，在编译时就知道他的内容了，其文本内容被硬编码到最终的可执行文件里
  - 速度快，效率高
- String类型，为了支持`可变性`，需要在heap上分配内存来保存编译时未知的文本内容：
  - OS必须在运行时请求内存
    - 通过String::from来实现
  - 当用完String后，需要通过某种方式将内存返回给OS
    - 这一步，在拥有GC的语言中，GC会跟踪并清理不在使用的内存
    - 没有GC，我们就需要去识别内存何时不在使用，并调用代码将它返回
- Rust采用了不同的方式，当变量走出作用范围时，内存会自动的交还给OS
  - drop函数



### 8.5.3 移动 Move

![image-20210601170710358](https://gitee.com/lzw657434763/pictures/raw/master/Blog/20210601170826.png)

```rust
fn main() {
    let mut s1 = String::from("hello");
    let s2 = s1;
}
```

- 浅拷贝 shallow copy
- 深拷贝 deep copy
- Move
  - 在Rust中，把s1赋值给s2的时候，s1就会失效，丧失所有权
  - Rust不会自动创建数据的深拷贝

### 8.5.4 克隆 Clone

- 如果真的想对heap上的String类型的数据进行深度拷贝，而不仅仅是stack上面的数据 ，可以使用clone方法

```rust
fn main() {
    let mut s1 = String::from("hello");
    let s2 = s1.clone();
}
```

![image-20210601171249739](https://gitee.com/lzw657434763/pictures/raw/master/Blog/20210601171255.png)

## 8.6 所有权与函数

- 在语义上，将值传递给函数和把值赋给变量是类似的
  - 将值传递给函数将发生移动和复制



- 一个变量的所有权总是遵循同样的模式：
  - 把一个值赋给其他变量时就会发生移动；
  - 当一个包含heap的数据的变量离开作用域时，他的值就会被drop函数清理，除非他的所有权移动到另一个变量上了





# 9. Struct

## 9.1 定义并实例化struct

```rust
fn main() {
    let mut user1 = User{
        username: String::from("lzw"),
        email: String::from("657434763@qq.com"),
        sign_in_count: 10,
        active: true
    };
    user1.email = String::from("123123123");

    let user2 = User{
        email: String::from("456456456"),
        ..user1
    };
    println!("{}",user1.email);
    println!("{}",user2.email);


}

struct User{
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```



## 9.2 struct方法

- 方法和函数类似：fn关键字，名称，参数，返回值

- 方法与函数的不同之处

  - 方法是在struct（或enum、trait对象）的上下文中定义

  - 第一个参数是self，表示方法被调用的struct实例

    ```rust
    struct Rect{
        width: u32,
        length: u32,
        area: u32,
    }
    
    impl Rect (){
        fn s(&self)->u32{
            self.width * self.length
        }
    }
    ```

    

