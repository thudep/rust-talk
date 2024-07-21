# 标准容器库

这里举一个例子, 对一堆数字 `1, 3, 4, 1, 2, 1` 统计每个数字出现的次数

```rust
use std::collections::HashMap;

fn main() {
    let nums = vec![1, 3, 4, 1, 2, 1];
    let mut counts = HashMap::new();
    let _: Vec<_> = nums
        .into_iter()
        .map(|i| {
            counts.entry(i).and_modify(|j| *j += 1).or_insert(1);
        })
        .collect();
    println!("{:?}", counts);
}
```