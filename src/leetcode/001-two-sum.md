# 1. 两数之和
题目链接: [两数之和](https://leetcode-cn.com/problems/two-sum/)

相信每个刷过LeetCode的人永远也忘不了这道题（就像当年背单词书永远也忘不了书中的第一个单词abandon哈哈哈），但是这道题用Rust来写也并不是那么简单，尤其是对于Rust新手，相信看完这道题你对Rust中的一些小细节会有更深的理解。


## 解法一
简单粗暴，双重循环，遍历所有的二元组直到找到符合题目要求的结果。

```rust
impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
        for i in 0..nums.len() {
            for j in (i + 1)..nums.len() {
                if nums[i] + nums[j] == target {
                    // 注意Rust中下标索引类型为usize，因此这里我们需要将i, j转换为i32
                    return vec![i as i32, j as i32];
                }
            }
        }
        vec![]
    }
}
```

## 解法二
我们观察解法一中第二个for循环：

```rust
for j in (i + 1)..nums.len() {
    if nums[i] + nums[j] == target {
        ...
    }
}
```

我们换种方式思考：

```rust
for j in (i + 1)..nums.len() {
    let other = target - nums[i];
    if nums[j] == other {
        ...
    }
}
```

因此我们的目标就变成了在一个数组中寻找值，这可是HashSet/HashMap的强项啊！由于这里我们还需要获取到对应值在数组中的下标索引位置，因此这里只能用HashMap了。

```rust
use std::collections::HashMap;

impl Solution {
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
        let mut map = HashMap::new();
        for (index, value) in nums.iter().enumerate() {
            let other = target - value;
            if let Some(&other_index) = map.get(&other) {
                return vec![other_index as i32, index as i32];
            }
            map.insert(value, index);
        }
        vec![]
    }
}
```

如果你是个Rust新手的话，相信你可能会存在以下几点疑惑：

- 为什么需要`use std::collections::HashMap;`这一行：可能是觉得HashMap用的没有那么多吧，因此Rust并没有将HashMap列入Prelude行列当中，因此当我们需要使用HashMap时，需要手动引入。
- 前面的`for i in 0..nums.len()`哪去了，这个`for (index, value) in nums.iter().enumerate()`是个什么鬼：在Rust中推荐使用迭代器，而enumerate这个迭代器适配器返回含有(下标，值)的元组。
- 为什么`if let Some(&other_index) = map.get(&other)`需要用到两个引用符号：简单来说，因为所有权的问题。[HashMap的get方法](https://doc.rust-lang.org/std/collections/struct.HashMap.html#method.get)中传入的参数是对key的引用，返回的是Option<&value>，因此我们需要用这两个引用符号。
