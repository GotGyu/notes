# 题型

## 二分查找

可以写成闭区间、开区间、或者左闭右开区间、或者左开右闭区间，但必须要全程统一，此处的开和闭指的是在本轮循环中是否要判断该数字

为了避免出现负数，使用**左闭右开区间**最方便。不用特判==target的时候返回

```rust
// 左闭右开写法
let (mut l, mut r) = (0, nums.len()); // [l,r)
// 区间不为空
while l < r {
    // 循环不变量：nums[left-1] < target, nums[right] >= target
    // r+l不溢出的话可以这样写，否则写成l+(r-l)/2更加保险
    let mid = (r+l)/2;
    if nums[mid] < target {
        l = mid + 1;
    } else {
        r = mid;
    }
}
return left;
```

```rust
// 闭区间写法
let (mut l, mut r) = (0, nums.len()-1); //[l,r]
while l <= r {
    // 循环不变量：nums[left-1] < target, nums[right+1] >= target
    let mid = (r+l)/2;
    if nums[mid] < target {
        l = mid + 1;
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

核心是**状态定义**和**状态转移方程**

启发思路：

- 选或不选
- 选哪个

题单：[分享丨【题单】动态规划（入门/背包/状态机/划分/区间/状压/数位/树形/数据结构优化） - 力扣（LeetCode）](https://leetcode.cn/circle/discuss/tXLS3i/)

### 0-1背包

#### 题型

有 n 个物品，第 i 个物品的体积为 `w[i]`，价值为 `v[i]`，每个物品至多选一个，求体积和不超过 `capacity` 的最大价值和

### 完全背包

#### 题型

有 n 种物品，第 i 种物品的体积为 `w[i]`，价值为 `v[i]`，每种物品**无限次**重复选，求体积和不超过 `capacity` 时的最大价值和

#### 模板

```rust
impl Solution {
    pub fn num_squares(n: i32) -> i32 {
        // 完全背包
        // dp[j] 表示要正好达到 j 的值，需要完全平方数至少 dp[j] 个
        // dp[j] = min(上一个dp[j], dp[j-i*i]+1)
        let n = n as usize;
        let max_val = n+1;
        // 初始化，dp[0]=0, 其他都为max_val
        let mut dp = vec![max_val; n+1];
        dp[0] = 0;
        // 遍历物品，需要i*i<=n
        // 将dp更新为min(目前达到j的完全平方数，距离达到j还有一个i*i的完全平方数)
        for i in 1..=(n as f64).sqrt() as usize {
            // 遍历背包，每切换一次i就会更新一轮dp数组，这一轮的dp数组均是距离j如果要先经历i代表的数的结果和上一轮dp数组取得的最小值，所以接下来就是看如果i取下一个数结果会不会变小
            // 这里的关键是正序遍历背包，状态叠加，更新的时候时在已更新的数据上继续更新，所以上一个数据已经用了i的话，下一个也能继续用，从而做到“无限”使用
            for j in i*i..=n {
                dp[j] = dp[j].min(dp[j-i*i]+1);
            }
        }
        if dp[n] == n+1 {
            return -1;
        }
        dp[n] as i32
    }
}
```

### 线性DP

线性 DP 通常强调**状态转移所依赖的前驱状态**是由给定数组所提供的，即拓扑序是由原数组直接给出，即通常有 `f[i]` 依赖于 `f[i−1]`

可以从第 `i` 个状态选或不选入手，开始写状态转移方程。如果选，则 `f[i]` 等于什么，如果不选，`f[i]` 等于什么。

### 序列DP

序列 DP 通常需要结合题意来寻找前驱状态，即**需要自身寻找拓扑序关系**，比如找到可转移的前驱状态 `f[i−m]`

### 计数类DP

### 最值类DP

题目要求求**最长**，有4个步骤：

1. 确定状态
   - 研究最优策略的最后一步
   - 化为子问题
2. 转移方程
   - 根据子问题定义得到
3. 初始条件和边界情况
4. 计算顺序

### 记忆化搜索

三步走：

1. 思考回溯要怎么写，可以画成一棵树（但回溯的时间复杂度是指数级别，需要优化）
   - 入参和返回值
   - 递归到哪里
   - 递归边界和入口
2. 改成记忆化搜索（自顶向下算）
   - 把树中重复的子树的结果记录下来
3. 1:1翻译成递推（自底向上算）
   - dfs -> f数组
   - 递归 -> 循环
   - 递归边界 -> 数组初始值

最后可以降低空间复杂度，使用滚动数组

## 堆

关键字：第k个

模式识别：维护**动态数据**的最大最小值，可以考虑堆

建立容量为k的大根堆/小根堆

## 回溯

关键：`dfs(i)` 处理的是从下标 `i` 到末尾的所有情况，包含 `dfs(i+1)`, `dfs(i+2)`, ... `dfs(n)`

每次执行步骤后都要**恢复现场**

## 图

### 判断有向图中是否有环

递归过程中，发现下一个节点已经在递归栈中（也就是正在访问中），则找到了环

对每个节点，需要定义三种状态值：

- 0 ：结点 x 尚未被访问到
- 1 ：结点 x 正在访问中，`dfs(x)` 尚未结束
- 2 ：结点 x 已经完全访问完毕，`dfs(x)` 已返回

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

`as_bytes()` 是将字符串转换为字节序列，这在处理非ASCII字符时会导致问题，更万全的方法是使用字符迭代：

```rust
// s:String
let s = s.chars().collect::<Vec<_>>();  
```



## 将 `HashMap` 转换为 `Vec`

很多数据结构都可以通过这种方法进行转换

``` rust
let mut v:Vec<(i32,i32)> = map.into_iter().collect();
```



# 技巧

## 常量额外空间/不需要额外空间

经常会涉及到位运算，可以往这个方向思考

位运算：

1. 异或：^
2. 

