[TOC]

# 基本类型

## 数值类型

### 有符号、无符号整数

#### count_ones

返回 `self` 二进制表示形式中1的个数

```rust
let number = (i as i32).count_ones();
```

### 在线程间安全共享的整数类型 AtomicU64

原子类型，用于在多线程环境中安全地操作 `u64` 类型的值，能够在不使用锁的情况下实现线程安全的操作，比使用锁的方案性能更高，同样还有其他类型值的原子版本，这里只记录 `u64` 的。

```rust
// 导入方式
use std::sync::atomic::AtomicU64
```

#### new

创建一个新的原子整数

```rust
let atomic = AtomicU64::new(42); //于是atomic=42
```

#### fetch_add

```rust
// 签名，需要一个 ordering 参数来描述操作的内存顺序
pub fn fetch_add(&self, val: u64, order: Ordering) -> u64
```

加到当前值，返回前一个值

```rust
let foo = AtomicU64::new(0);
let ret = foo.fetch_add(10, Ordering::SeqCst);  // ret=0, foo=10
```



## 字符 char

### is_lowercase/is_uppercase

如果该 `char` 具有 `Lowercase` /`Uppercase `属性，则返回 `true`

```rust
assert!('a'.is_lowercase());  // true
assert!('δ'.is_lowercase());  // false
```

### make_ascii_lowercase/make_ascii_uppercase

将此类型**就地转换**为其 ASCII 小写/大写等效项，非 ASCII 字母不变

```rust
let mut ascii = 'a';
ascii.make_ascii_uppercase();
assert_eq!('A', ascii);
```

### to_ascii_lowercase/to_ascii_uppercase

使值的**副本**等效于其 ASCII 小写/大写字母，非 ASCII 字母不变

```rust
let ascii = 'a';
assert_eq!('A', ascii.to_ascii_uppercase());
```

## 布尔 bool

## 单元类型 ()

单元类型就是 `()`，不占用任何内存

## 关键字

### async

Rust选择的异步编程模型。通过 `async` 标记的语法块会被转换成实现了 `Future` trait 的状态机。**返回封装了函数返回值的 `Future`**，而不是阻塞当前线程，会让出当前线程的控制权，让其他 `Future` 在该线程中运行。

### await

调用者可以使用 `.await` 来暂停执行、让出线程，直到 `Future` 的结果准备就绪为止，并且还解析出最终的返回值。相当于对 `executor` 说：“等这个 `Future` 完成后再恢复我”。

### continue

跳到循环的下一个迭代，遇到 `continue` 时，当前迭代终止，将控制权返回到循环头

### dyn

是 trait 对象类型的前缀，用于强调关联 trait 上的方法是动态分配的（`impl` 是静态分配）。由于编译器不知道被传递的具体类型，所有 `dyn` trait 引用包含两个指针，一个指针指向方法调用名称与函数指针的映射。

由于 Rust 编译器需要知道每个函数的返回类型需要多少空间，即所有函数都必须返回一个具体类型，以下代码中有 `Animal` trait，但不能编写返回 `Animal` 的函数，因为其不同的实现需要不同的内存量，所以可以选择返回一个包含一些 `Animal` 的 `Box`，`Box` 是一个引用，引用的大小是静态已知的。因此，如果函数需要返回指向堆的 trait 的指针，需要使用 `dyn` 编写返回类型。

```rust
pub trait Animal {
    fn noise(&self);
}

pub struct Sheep {}

pub struct Cow {}

impl Animal for Sheep {
    fn noise(&self){
        println!("mamamama!");
    }
}

impl Animal for Cow {
    fn noise(&self) {
        println!("moooooo!");
    }
}

fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {})
    } else {
        Box::new(Cow {})
    }
}
```

### ref

在使用 `let` 绑定进行模式匹配或解构时，可以使用 `ref` 关键字来获取结构体或元组字段的**引用**。下面的代码是只能使用 `ref` 而不能使用 `&` 的情况，如果 `t` 前面不加 `ref`，则无法编译，因为 `s` 的所有权转交给 `t` 了

```rust
let s = Some(String::from("Hello"));
match s {
    Some(ref t) => println!("t={}", t);
    _ => {}
}
println!("s={}", s.unwrap());
```

或者可以这样写：

```rust
let s = Some(String::from("Hello"));
match &s {
    Some(t) => println!("t={}", t);
    _ => {}
}
println!("s={}", s.unwrap());
```

赋值左侧的 `ref` 借用等同于右侧的 `&` 借用，也就是下面两句相等：

```rust
let ref ref_c1 = c;
let ref_c1 = &c;
```



### static

静态项，在程序的整个持续时间内有效，不会在程序结束时调用 `drop`，静态项不能移动，可以修改

# 复合类型

## 固定指针 Pin

```rust
// 实现
pub struct Pin<P> {
    pointer: P,
}
```

包装一个指针，并且能确保该指针指向的数据不会被移动，除非它实现 `Unpin`

## 切片 slice

切片是一个内存块的视图，表示为一个指针和一个长度，也就是**引用集合中部分连续的元素序列**，而非整个集合。切片是可变的或共享的。 共享切片类型为 `&[T]`，而可变切片类型为 `&mut [T]`，如

```rust
let s = String::from("hello world");
// 创建切片，左闭右开区间
let hello = &s[0..5];
let world = &s[6..11];
```

### binary_search

```rust
// 函数签名
pub fn binary_search(&self, x: &T) -> Result<usize, usize>
where
    T: Ord,
```

在给定切片中搜索给定元素，切片需要经过排序。如果找到该值，则返回 `Result::Ok` 表示匹配元素的索引，如果有多个则可以返回任何一个索引；如果找不到，则返回 `Result::Err`，其中包含在保留排序顺序的同时可以在其中插入匹配元素的索引

```rust
let s = [0,1,2,3,5,13];
assert_eq!(s.binary_search(&13),  Ok(5));
assert_eq!(s.binary_search(&4),   Err(4));
```



### join

将 `T` 的切片展平为单个值 `Self::Output`，并在每个值之间放置一个给定的分隔符

```rust
assert_eq!(["hello", "world","Mary"].join(" "), "hello world Mary");
```

### sort_unstable_by

```rust
// 函数签名
pub fn sort_unstable_by<F>(&mut self, compare: F) 
where 
	F: FnMut(&T, &T) -> Ordering
```

使用比较器函数对切片进行不稳定排序，比较器函数必须为切片中的元素定义总顺序

```rust
// 对v进行反向排序
let mut v = [5,4,1,3,2];
v.sort_unstable_by(|a, b| b.cmp(a));  //v=[5,4,3,2,1]
```



## 字符串切片 str

`str` 类型，是最原始的字符串类型，通常以其借用形式 `&str` 出现，也是字符串字面量的类型，`&'static str`，比如以下就是一个字符串切片：

```rust
let s = "Hello, World"; //此处s的类型是&str
```

`str` 类型被硬编码进可执行文件，无法被修改，但 `String` 是可增长、可改变且具有所有权的 `UTF-8` 编码字符串（即字符串中的字符所占的字节数是变化的(1 - 4)），`&str` 也是 `UTF-8` 编码字符串

### bytes

返回字符串切片的**字节**上的迭代器，用来通过字节遍历字符串切片

```rust
// 计算一个str中字符的ascii码之和
for i in s.bytes() {
    sum += i as u8;
}
```

### chars

返回字符串切片的 `char` 上的迭代器，用来通过 `char` 遍历字符串切片，每次调用都会返回下一个未被遍历的**字符**

```rust
// 使用 enumerate 获取字符与其位置
for (i,val) in str.chars().enumerate() {...} // i为索引下标，val为对应位置值
```

### split_whitespace

用空格分割str，返回的迭代器指向每一个str，任意连续空格被视作一个空格

```rust
let mut iter = "Mary   a word".split_whitespace()；
assert_eq!(Some("Mary"), iter.next());
assert_eq!(Some("a"), iter.next());
assert_eq!(Some("word"), iter.next());
```



## 字符串 String

UTF-8编码的可增长字符串，取引用则转换为 `&str`

### as_bytes

将 `String` 转换为字节切片，不会进行内存的重新分配，只是返回对原始数据的借用，因此字节切片的生命周期与原始 `String` 对象绑定，没有转移所有权

适用场景：只需要字节的不可变引用，且不需要修改原始 `String` 时，选择 `as_bytes` 更高效（不用内存分配）

```rust
let s = String::from("hello");  // s为&[104, 101, 108, 108, 111]
```

### as_str

提取包含整个 `String` 的字符串切片

```rust
let s = String::from("foo");
assert_eq!("foo", s.as_str());  //s.as_str()为&str类型
```

### find

```rust
// 函数签名
// pat可以是&str, char, char的切片，以及确定字符是否匹配的函数或闭包
pub fn find<'a, P>(&'a self, pat: P) -> Option<usize>
where
    P: Pattern<'a>,
```

返回字符串切片中与模式匹配的**第一个**字符的字节索引，模式不匹配则返回 `None`

```rust
// 查找某字符第一次出现的下标
if let Some(x) = s.find(|&c| c==val);
```

### from

从字符串字面量（`&str`）创建一个 `String`

```rust
let hello = String::from("Hello, world!"); // hello是String类型
```

### into_bytes

将 `String` 转换为字节vector，会消耗 `String`，并将数据转换为一个新的 `Vec`，所以调用 `into_bytes` 后，原 `String` 的所有权被转移，不能再使用原 `String` 对象

适用场景：需要拥有字节数组的所有权，且不需要原始的 `String` 时

```rust
let s = String::from("hello");
let bytes = s.into_bytes();  // bytes=[104,101,108,108,111]
```

### push_str

将给定的字符串切片 `&str` 原地追加到这个 `String` 的末尾

```rust
let mut s = String::from("foo");
s.push_str("bar");  // s:String=foobar
```

## 数组 array

## 

# 集合类型

## 二进制堆 BinaryHeap

大根堆（任一节点大于其子节点的值），每次更改堆结构都会重新维护为大根堆

导入：`use std::collections::BinaryHeap;`

### min-heap

`std::cmp::Reverse` 或自定义 `Ord` 实现可用于使 `BinaryHeap` 成为小根堆，从而`heap.pop()` 返回最小值而非最大值

``` rust
use std::collections::BinaryHeap;
use std::cmp::Reverse;

let mut heap = BinaryHeap::new();

// 在 `Reverse` 中包装值
heap.push(Reverse(1));
heap.push(Reverse(5));
heap.push(Reverse(2));

// 如果我们现在弹出这些分数，它们应该以相反的顺序返回。
assert_eq!(heap.pop(), Some(Reverse(1)));
assert_eq!(heap.pop(), Some(Reverse(2)));
assert_eq!(heap.pop(), Some(Reverse(5)));
assert_eq!(heap.pop(), None);
```

### from

可以从数组初始化具有已知项列表的 `BinaryHeap`

```rust
let heap = BinaryHeap::from([1, 5, 2]);
```

### peek

返回 `BinaryHeap` 中最大的项，若为空则返回 `None`

```rust
if let Some(x) = heap.peek();
```

### pop

从`BinaryHeap` 中**删除**最大的项并返回它，若为空则返回 `None`

``` rust
if let Some(x) = heap.peek();
```

### push

将项推入 `BinaryHeap` 。预期成本是 *O*(1)，该成本是在被推元素的每个可能排序以及足够大量的推数上平均的，当元素尚未处于任何排序模式的元素时，这是最有意义的成本指标；如果元素主要以升序推入，则时间复杂度会升高。

```rust
heap.push(5);
```

## B树 BTreeMap

基于B树的**有序**map，支持范围查询和按顺序遍历，非常适合需要**按顺序访问键值对**的场景。

使用B树作为底层数据结构，是一种自平衡树，表示缓存效率与实际最小化搜索中执行的工作量之间的根本折衷，其特性使得在大多数操作（查找、插入、删除）上的时间复杂度为 `O(log n)`

## Bset BTreeSet

基于B树的**有序**set，其中的元素需要实现 `Ord`, `PartialOrd` trait

## 哈希表 HashMap

导入：`use std::collections::HashMap;`

### entry

在HashMap中获取给定键的对应项，以进行原地操作

```rust
// 计数：将map中键c对应的值增加1。如果键c不存在，则插入该键并初始化值为0，然后将值加1
*map.entry(c).or_insert(0) += 1;
```

### get

返回与键对应的值的**引用**，键需要以**借用形式**提供

```rust
assert_eq!(map.get(&1), Some(&"a"));
```

### from_iter

从迭代器构建 `HashMap`

```rust
let map = HashMap::from_iter(vec);
```



### keys

以任意顺序访问HashMap所有键的迭代器，可以遍历HashMap的key

```rust
for value in map.keys() {
    //...
}
```

### values

以任意顺序访问HashMap所有值的迭代器，可以遍历HashMap的value

```rust
for value in map.values() {
    //...
}
```

### 哈希集 HashSet

一种基于哈希表的数据结构，通过哈希函数来组织数据，以支持快速的插入和搜索操作。哈希集中每个元素都是唯一的，不允许重复，与插入顺序无关。

## 双端队列 VecDeque

使用可增长的环形缓冲区实现的双端队列

### from

从数组初始化具有已知项列表的 `VecDeque`

```rust
let deq = VecDeque::from([-1, 0, 1]);
```

### pop_back/pop_front

从双端队列中移除最后一个/第一个元素并返回它，如果为空则返回 `None`

```rust
deq.pop_back(1);
deq.pop_front(2);
```

### push_back/push_front

将元素追加到双端队列的前面/后面

```rust
deq.push_back(1);
deq.push_front(2);
```

## 动态数组 Vector

### into_iter

返回 `Vec` 上的实现 `IntoIterator` 特质的迭代器，该迭代器遍历 `Vec` 中的元素并返回它们的**所有权**，即元素本身，类型为 `T`，也即该方法会消耗 `Vec`，迭代结束后，原始的 `Vec` 不再拥有所有权，变为空。

### iter （比 into_iter更高效）

返回 `Vec` 上的实现 `Iterator` 特质的迭代器，该迭代器遍历 `Vec` 中的元素并返回它们的**不可变引用**，遍历获得项时数据类型是 `&T`，即返回的是引用而不是值本身，原因有二：

1. **性能**：返回引用可以避免不必要的复制，特别是当元素类型较大或复制成本较高时。
2. **所有权**：`Vec` 仍然拥有其元素的所有权，而迭代器只是提供了一种访问这些元素的方式，通过返回引用，Rust 能够确保 `Vec` 的所有权语义不会被破坏。

```rust
for &x in v.iter() {
    // 此时x是T类型
    //...
}
```



### resize

``` rust
// 函数签名
pub fn resize(&mut self, new_len: usize, value:T)
```

调整 `Vec` 的大小，使得 `len = new_len`，如果 `new_len` 大于 `len`，那么`Vec` 将会向后扩充，并用 `value` 填满，否则将 `Vec` 截断。

`T` 需要实现 `Clone`

```rust
let mut vec = vec!["hello"];
vec.resize(3, "world");
assert_eq!(vec, ["hello", "world", "world"]);
```

# 错误处理

## expect

返回包含 `self` 值的 `Some` 值，如果 `self` 为 `None` 则会 `panics`，并输出由 `msg` 提供的自定义 `panic` 消息

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

## Option

### and_then

将一个可能为 `None` 或 `Err` 的值转换为另一个可能为 `None` 或 `Err`的值，同时避免嵌套的 `match` 或 `if let` 语句，支持链式调用。

- 如果当前值是 `Some` 或 `Ok`，则对其内部值应用一个函数，并返回该函数的结果
- 如果当前值是 `None` 或 `Err`，则直接返回

适合处理需要连续操作多个可能失败的计算的场景

```rust
fn divide(a: f64, b: f64) -> Option<f64> {
    if b == 0.0 {
        None
    } else {
        Some(a / b)
    }
}

fn main() {
    let result = Some(10.0)
        .and_then(|x| divide(x, 2.0)) // 10.0 / 2.0 = 5.0
        .and_then(|x| divide(x, 0.0)); // 5.0 / 0.0 = None

    println!("{:?}", result); // 输出: None
}
```



### as_mut

将 `&mut Option<T>` 转换为 `Option<&mut T>`

```rust
let mut x = Some(2);
match x.as_mut() {
    Some(v) => *v = 42,
    None => {},
}
assert_eq!(x, Some(42));
```

### as_ref

从 `&Option<T>` 转换为 `Option<&T>`

### is_some

如果是 `Some` 值，返回 `true`，否则为 `false`

## unwrap

返回包含 `self` 值的 `Some` 值，如果 `self` 为 `None`则会 `panics`，所以通常不建议使用

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").unwrap();
}
```



# 智能指针

智能主要体现在实现了 `Deref` 和 `Drop` 特征

## 原子引用计数 Arc

原子化的 `Rc<T>` 智能指针，与 `Rc<T>` 功能相似但是多线程安全。原子化是一种并发原语，它能保证数据安全的在线程间共享。比 `Rc<T>` 性能损耗大不少，和 `Rc` 拥有完全一样的 API，故全记录在 `Rc`章节中。

在多线程编程中，`Arc` 与 `Mutex` 锁的组合使用非常常见，这样既可以在不同的线程中共享数据，又允许在各个线程中对其进行修改。

```rust
// 引入方式
use std::sync::Arc
```

## 堆对象分配 Box

`Box<T>` 允许将一个值分配到堆上，然后在栈上保留一个智能指针指向堆上的数据。是比较简单、高效的智能指针使用场景：

- 特意的将数据分配在堆上
- 数据较大时，又不想在转移所有权时进行数据拷贝
- 类型的大小在编译期无法确定，但是我们又需要固定大小的类型时
- 特征对象，用于说明对象实现了一个特征，而不是某个特定的类型

## RefCell

用于解决可变、不可变引用共存的需求，实现编译期可变、不可变引用共存，但违背借用规则仍然会在程序运行期 `panic`，适用于编译期误报或者一个引用被在多处代码使用、修改以至于难于管理借用关系时。主要用于实现内部可变性。

```rust
use std::cell::RefCell;
use std::rc::Rc;
fn main() {
    let s = Rc::new(RefCell::new("我很善变，还拥有多个主人".to_string()));

    let s1 = s.clone();
    let s2 = s.clone();
    // let mut s2 = s.borrow_mut();
    s2.borrow_mut().push_str(", oh yeah!");

    println!("{:?}\n{:?}\n{:?}", s, s1, s2);
}

// 运行结果：
// RefCell { value: "我很善变，还拥有多个主人, oh yeah!" }
// RefCell { value: "我很善变，还拥有多个主人, oh yeah!" }
// RefCell { value: "我很善变，还拥有多个主人, oh yeah!" }
```



## 引用计数 Rc

引用计数，Reference Counting，通过记录一个数据被引用的次数来确定该数据是否正在被使用，当次数归零时，就代表该数据不再被使用，因此可以清理释放。当**希望在堆上分配一个对象供程序的多个部分使用，且无法确定哪个部分最后一个结束**时，就可以使用 `Rc` 成为数据值的所有者，比如多线程场景。

`Rc<T>` 是指向底层数据的不可变引用，无法通过它来修改数据。若需修改，需配合其他数据类型，如内部可变性的 `RefCell<T>`、互斥锁 `Mutex<T>`。一旦最后一个拥有者小时，资源会自动回收，生命周期在编译器就会被确定。`Rc` 只能用于同一线程内部（因为没有实现 `Send` 特征），如果需要跨线程就需使用 `Arc`。`Rc<T>` 是一个实现了 `Deref` 特征的智能指针，使用 `T` 时无需先解开 `Rc` 指针，而可以直接使用。

```rust
// 引入方式
use std::rc::Rc
```

### clone

浅拷贝

### strong_count

`Rc<T>` 在创建时，会将引用计数+1，此时执行 `Rc::strong_count` 会返回 1



## 互斥锁 Mutex

同一时间，只允许一个线程访问该值，其他线程需要等待其访问完成后才能继续

```rust
use std::sync::Mutex;
```

### new

创建新的互斥锁实例

```rust
let m = Mutex::new(5);
```

### lock

要使用 `m.lock()` 方法向 `m=Mutex::new(5)` 申请一个锁，该方法会**阻塞当前线程，直至获取到锁**。可能会报错，如当前正持有锁的线程 `panic` 了。该方法返回一个智能指针 `MutexGuard<T>`，它实现了 `Deref` 特征和 `Drop` 特征

```rust
{
    // 获取锁，然后deref为`m`的引用
    // lock返回的是Result
    let mut num = m.lock().unwrap();
    *num = 6;
    // 锁自动被drop
}
```



# 其他类型

## Entry

`map` 中单个条目的视图，根据 `entry` 方法构造，可能是占用条目可能是空条目

### and_modify

在任何插入 map 之前，提供对占用条目的就地**可变访问**

```rust
map.entry("poneyland").and_modify(|e| {*e+=1}).or_insert(42);//map中存储(poneyland, 42)，下一次执行则是(poneyland, 43)
```

### or_insert

如果为空，则通过插入默认值来确保该值在条目中，并返回对条目中值的可变引用

```rust
map.entry("poneyland").or_insert(10); //map中存储(poneyland, 10)
```

## PhantomData

一个标记类型，用于在类型系统中表示某种“假想”的数据关系，**实际上并不占用任何内存空间**。

## 类型别名 type

类型别名不是一个独立的全新的类型，而是某一个类型的别名，使用它只是为了让可读性更好，比如：

```rust
type Meters = u32;
```

则编译器会把 `Meters` 当作 `u32` 来使用

# Trait

## Debug

用于格式化输出一个类型的调试信息，`println!("{:?}", value)` 通过 `{:?}` 格式化占位符来打印实现了 `Debug` trait 的值。使用 `#[derive(Debug)]` 会自动为结构体或枚举（需要其中所有字段都实现 `Debug` trait）生成 `Debug` trait 的实现代码

## Deref

**只能为智能指针实现**的 trait，允许自定义类型像普通引用一样被解引用（`*` 运算符），使智能指针的行为类似于普通引用。

```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;  // 关联类型，指向的目标类型

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

fn main() {
    let x = MyBox(5);
    assert_eq!(*x, 5);  // `*x` 实际上调用了 `deref`，变成 `*(x.deref())`
}

```

一个类型为 `T` 的对象 `foo`，如果 `T: Deref<Target=U>`，那么，相关 `foo` 的**引用** `&foo` 在应用的时候会自动转换为 `&U`。仅引用类型的实参才会触发自动解引用，支持连续的隐式转换，如 `Box<String> -> String -> &str`

## Display

### ToString

一个用于将值转换为 `String` 的 trait。对于任何实现 `Display` trait 的类型，都会自动实现 `ToString` trait。 因此，要想获得 `ToString`，应该实现 `Display` 

```rust
let s = x.to_string();
```

## Drop

用于回收内存资源、执行收尾工作，Rust 为几乎所有类型都实现了 `Drop` trait，但是有极少数情况，需要你自己来回收资源的，例如文件描述符、网络 socket 等，需要手动 `Drop`。`Copy` 和 `Drop` 无法同时实现在一个类型上。

## Future

异步编程的核心，可以理解为一个在未来某个时间点被调度执行的任务。

## 迭代器 Iterator

### collect

将迭代器转换为集合，可以将任何可迭代的东西变成一个相关的集合，可以用 `collect::<>` 指定集合类型，比如 `iter.collect::<Vec<_>>` 即转换为 `Vec` 类型

```rust
// 基本用法
let a = [1, 2, 3];
let doubled: Vec<i32> = a.iter()
                         .map(|&x| x * 2)
                         .collect();
assert_eq!(vec![2, 4, 6], doubled);
```

### count

消耗迭代器，计算迭代次数并返回该值。此方法将反复调用 `next`，直到遇到 `None`，并返回它看到 `Some` 的次数。

```rust
let a = [1, 2, 3];
assert_eq!(a.iter().count(), 3);
```

### fold

需要两个参数：

- 初始值
- 闭包：闭包含有两个参数，累加器、元素

闭包返回累加器在下一次迭代中应具有的值，初始值是累加器在第一次调用时将具有的值。`fold()` 通过应用操作将每个元素 `fold` 到一个累加器中，返回最终结果

```rust
let a = [1,2,3];
let sum = a.iter().fold(0, |acc, x| acc+x);  //sum=6
// 0是初始值，|acc,x|是闭包，acc是迭代器，x是元素，acc+x是操作
```



### enumerate

创建迭代器，返回当前迭代次数以及下一个值的对 `(i, val)`，`i:usize` 是当前迭代索引，`val` 是值

```rust
let a = ['a', 'b', 'c'];
let mut iter = a.iter().enumerate();
assert_eq!(iter.next(), Some((0, &'a')));
```

### map

获取一个闭包并创建一个迭代器，该迭代器在每个元素上调用该闭包。由于 `map` 是惰性的，因此在已经使用其他迭代器时，最好使用 `map()`。

```rust
let new = old.iter().map(|x| x+1).collect(); // new里面是old每个元素+1
```



### next

推进迭代器并返回下一个值，迭代完成后返回 `None`

```rust
let a = [1, 2, 3];
let mut iter = a.iter();
assert_eq!(Some(&1), iter.next());
```

### nth

返回迭代器的第n个元素，计数从0开始，即 `nth(0)` 返回第一个值。所有先前的元素以及返回的元素都将从迭代器中消耗，即经过调用就被丢弃，若在同一迭代器上多次调用 `nth(0)` 将返回不同的元素。函数签名返回 `Option<Self::Item>`

```rust
let a = [1, 2, 3];
assert_eq!(a.iter().nth(1), Some(&2));
```

### rev

反转迭代器的方向，改为从右向左进行迭代

```rust
// 将String倒序输出
let ans = s.chars().rev().collect()
```

### take

```rust
fn take(self, n:usize) -> Take<Self>
```

创建一个迭代器，`take(n)` 返回包含前`n` 个元素的迭代器，如果个数小于 `n` ，就返回所有元素

### zip

将参数转换为迭代器，将两个迭代器的元素配对，组成一个元组，最终返回一个迭代器，支持嵌套链接使用

```rust
A.iter().zip(B.iter().zip(C.iter()))
// 会返回迭代器，指向的元素为(a,(b,c))
```





## Stream

类似于 `Future` trait，但在完成前可以生成多个值，这一点类似于 `Iterator` trait

```rust
// 实现
trait Stream {
    // Stream生成的值的类型
    type Item;

    // 尝试去解析Stream中的下一个值,
    // 若无数据，返回`Poll::Pending`,
    // 若有数据，返回 `Poll::Ready(Some(x))`
    // `Stream`完成则返回 `Poll::Ready(None)`
    fn poll_next(self: Pin<&mut Self>, cx: &mut Context<'_>)
        -> Poll<Option<Self::Item>>;
}
```

可以对 `Stream` 进行迭代访问，例如使用 `map, filter, fold` 等方法。可以用 `while let, next, try_next`，但不可以用 `for` 循环

```rust
// 一个Stream并发处理多个值
// 入参 stream 是一个指向流的可变引用，每个元素的类型是 Result<u8, io::Error>
async fn jump_around(
    mut stream: Pin<&mut dyn Stream<Item = Result<u8, io::Error>>>,
) -> Result<(), io::Error> {
    use futures::stream::TryStreamExt; // 引入这个trait为流提供了许多辅助函数，比如 `try_for_each_concurrent`用于并发地处理流中的元素
    const MAX_CONCURRENT_JUMPERS: usize = 100;

    stream.try_for_each_concurrent(MAX_CONCURRENT_JUMPERS, |num| async move {
        // 此处是对流的每个元素进行的异步操作
        jump_n_times(num).await?;
        report_n_jumps(num).await?;
        Ok(())
    }).await?;

    Ok(())
}
```



# 宏

## 输入输出

### format

使用运行时表达式的插值创建 `String`，收到的第一个参数是格式字符串，其他参数将以给定的顺序替换格式字符串中的 `{}`

若要将单个值转换为 `String`，可以使用 `to_string` 方法

```rust
// 将数字以时间形式放进vec中
vec.push(format!("{}:{}", i, j));
```

## join

**同时**轮询多个 futures，完成后，返回所有结果的元组。虽然 `join!(a, b).await` 类似于 `(a.await, b.await)`，但 `join!` 会同时轮询两个 futures，因此效率更高。

```rust
#![feature(future_join)]

use std::future::join;

async fn one() -> usize { 1 }
async fn two() -> usize { 2 }

let x = join!(one(), two()).await;
assert_eq!(x, (1, 2));
```

## panic

一定是遇到**不可恢复的错误**时才调用 `panic!` 处理。

若你确切的知道你的程序是正确时，也可以使用 `unwrap`、 `panic` 等处理，因为 `panic` 的触发方式比写错误处理要简单，可以让代码更简洁、好读。比如：

```rust
use std::net::IpAddr;
let home: IpAddr = "127.0.0.1".parse().unwrap();
```

此时知道 `parse` 方法一定会成功，就可以直接调用 `unwrap`，但如果这个字符串来自用户输入，在实际项目中，就必须用错误处理。

## ?

一个适合用来**传播Result、转换错误类型**的宏，作用类似于 `match`，如果结果是 `Ok(T)`，就把 `T` 赋值给变量，如果结果是 `Err(E)`，那就返回错误。原理在于隐式调用了 `from` 方法，支持链式调用。

```rust
fn last_char_of_first_line(text: &str) -> Option<char> {
    text.lines().next()?.chars().last()
}
```

由于 `?` 要么返回 `None`，要么返回 `T`（而非 `Some<T>`），所以需要一个变量来承载正确的值，因此以下写法是错误的：

```rust
fn first(arr: &[i32]) -> Option<&i32> {
   arr.get(0)? // 在0号元素存在的时候返回类型为&i32
}
```

自动解引用，比如在列表中，`q` 是一个 `ListNode` 结点，那么：

- `q.next` 返回 `Option<Box<ListNode>>`
- `q.next.as_ref()` 会从 `Option` 中获得内部值的引用，即转换为 `Option<&Box<ListNode>>`
- `q.next.as_ref()?` ：
  - 如果 `q.next`是 `None`，则 `?`会提前返回 `None`，避免对 `None`进行解引用
  - 如果 `q.next`是 `Some(Box<ListNode>)`，则 `?`会解引用 `&Box<ListNode>`，得到 `&ListNode`
