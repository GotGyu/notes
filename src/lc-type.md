# 题型

## 二分查找

```rust
let (mut l, mut r) = (1, n);
while l < r {
    let mid = l+(r-l)/2;
    if ... {
        l = mid;
    } else {
        r = mid - 1;
    }
}
return left;
```

## 单调栈

触发关键词**下一个更大元素**

宗旨：及时去掉无用数据，保证栈中数据有序

### 从右到左

从右往左倒着遍历，栈中记录下一个更大元素的**候选项**。由于左边更大元素会**挡住**右边更小的元素，所以右边更小的元素是**无用信息**（也就是，不会成为左边元素的下一个更大元素），这会导致**栈底（右边）大，栈顶（左边）小**

```rust
use std::collections::HashMap;
let mut ans = vec![0;n];
// 候选栈
let mut st = vec![];
for (i, &t) in input.iter().enumerate().rev() {
    // 新元素值更大则弹出右边的小元素
    while !st.is_empty() && t>=input[st[st.len()-1]] {
        st.pop();
    }
    // 存入新的左边大元素
    st.push(i)
}
```

### 从左到右

栈中记录**还没算出**下一个更大元素的那些数，只要遍历到比栈顶元素更大的数，就意味着栈顶元素找到了答案，记录答案，并弹出栈顶。

```rust
use std::collections::HashMap;
let mut ans = vec![0;n];
// 相当于栈是一个todolist
let mut st = vec![];
for (i, &t) in input.iter().enumerate() {
    while !st.is_empty() && t>input[st[st.len()-1]] {
        //...
    }
    st.push(i)
}
```

### 题单

496

## 转换进制

递归，以转换为7进制为例，针对 `num<0` 和 `num<7` 分别处理，之后进行递归，不断**拼接余数**到字符串

```rust
pub fn convert_to_base7(num: i32)-> String {
    if num < 0 {
        return format!("-{}",convert to base7(-num));
    }
    if num < 7 {
        return format!("{}",num);
    }
	format!("{}{}", convert to base7(num/7), convert to_base7(num%7))
}
```

### 题单

504

## 动态规划

核心是状态定义和状态转移方程



# 部分功能代码

## 通过下标访问 `String`

Rust 中是不允许通过下标来获取 `String` 的字符的，即若有 `s="Hello".to_string()`，`s[0]`,`s[1]`之类的写法无法通过编译。但这样会造成很多不便，想要实现这个功能时可以：

``` rust
// 将 s:String 转换为 u8 字节切片
let S = s.as_bytes();
// 通过下标访问 S
count[S[pos] as usize] += 1;
...
```

