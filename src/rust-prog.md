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



# Trait

## 迭代器 Iterator

### enumerate

创建迭代器，返回当前迭代次数以及下一个值的对 `(i, val)`，`i:usize` 是当前迭代索引，`val` 是值