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

