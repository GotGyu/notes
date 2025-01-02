[TOC]

# 数据结构

## 字符串 String

UTF-8编码的可增长字符串

### bytes

返回字符串切片的**字节**上的迭代器，用来通过字节遍历字符串切片

```rust
// 计算一个string中字符的ascii码之和
for i in s.bytes() {
    sum += i as u8;
}
```

### chars

返回字符串切片的 `char` 上的迭代器，用来通过 `char` 遍历字符串切片，每次调用都会返回下一个未被遍历的字符

```rust
// 使用 enumerate 获取字符与其位置
for (i,val) in str.chars().enumerate() {...} // i为索引下标，val为对应位置值
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



# 基本类型

## 有符号、无符号整数

### count_ones

返回 `self` 二进制表示形式中1的个数

```rust
let number = (i as i32).count_ones();
```



# Trait

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

### enumerate

创建迭代器，返回当前迭代次数以及下一个值的对 `(i, val)`，`i:usize` 是当前迭代索引，`val` 是值

# 宏

## 输入输出

### format

使用运行时表达式的插值创建 `String`，收到的第一个参数是格式字符串，其他参数将以给定的顺序替换格式字符串中的 `{}`

```rust
// 将数字以时间形式放进vec中
vec.push(format!("{}:{}", i, j));
```

