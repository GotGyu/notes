[TOC]

# 基本类型

## 数值类型

### 有符号、无符号整数

#### count_ones

返回 `self` 二进制表示形式中1的个数

```rust
let number = (i as i32).count_ones();
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

### continue

跳到循环的下一个迭代，遇到 `continue` 时，当前迭代终止，将控制权返回到循环头

# 复合类型

## 切片 slice

切片是一个内存块的视图，表示为一个指针和一个长度，也就是**引用集合中部分连续的元素序列**，而非整个集合。切片是可变的或共享的。 共享切片类型为 `&[T]`，而可变切片类型为 `&mut [T]`，如

```rust
let s = String::from("hello world");
// 创建切片，左闭右开区间
let hello = &s[0..5];
let world = &s[6..11];
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

## 哈希表 HashMap

导入：use std::collections::HashMap

### entry

在HashMap中获取给定键的对应项，以进行原地操作

```rust
// 计数：将map中键c对应的值增加1。如果键c不存在，则插入该键并初始化值为0，然后将值加1
*map.entry(c).or_insert(0) += 1;
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

# Trait

## Display

### ToString

一个用于将值转换为 `String` 的 trait。对于任何实现 `Display` trait 的类型，都会自动实现 `ToString` trait。 因此，要想获得 `ToString`，应该实现 `Display` 

```rust
let s = x.to_string();
```

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

### enumerate

创建迭代器，返回当前迭代次数以及下一个值的对 `(i, val)`，`i:usize` 是当前迭代索引，`val` 是值

```rust
let a = ['a', 'b', 'c'];
let mut iter = a.iter().enumerate();
assert_eq!(iter.next(), Some((0, &'a')));
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



# 宏

## 输入输出

### format

使用运行时表达式的插值创建 `String`，收到的第一个参数是格式字符串，其他参数将以给定的顺序替换格式字符串中的 `{}`

若要将单个值转换为 `String`，可以使用 `to_string` 方法

```rust
// 将数字以时间形式放进vec中
vec.push(format!("{}:{}", i, j));
```

