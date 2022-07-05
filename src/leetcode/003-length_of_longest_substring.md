# 3. 无重复字符的最长子串
题目链接：[无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

这题的主要思路是：滑动窗口。

滑动窗口是一种基于双指针的一种思想，两个指针指向的元素之间形成一个窗口。此题中运用的是大小动态变化的窗口。

## 滑动窗口解法
先初始化一个容量和左右指针都为0的滑动窗口，此窗口的容量随左右指针的移动而变化。

窗口内就是无重复字符的子串。这就要求右指针在前进的过程中，每一步都要确保其指向的字符不能出现在窗口内。所以如果出现在了窗口内，就要向右移动左指针，缩小窗口，使窗口不包含重复字符。

保留每一步的窗口长度，取最大值，就是此题答案。

```rust
use std::collections::HashMap;

impl Solution { // 滑动窗口的应用
    pub fn length_of_longest_substring(s: String) -> i32 {
        let mut map = HashMap::new(); // k：字符。v：字符最后出现的索引
        let mut max = 0;
        let mut l = 0;
        for (r, c) in s.char_indices() {
            if let Some(end) = map.insert(c, r) { // end：上一次该字符最后出现的索引
                l = usize::max(l, end + 1); // 只有当end+1出现在窗口内时，滑动窗口的左边界才更新为end+1
            }
            max = usize::max(r - l + 1, max);
        }
        max as i32
    }
}
```

我在解题时遇到的问题。

1. `String`类型的`char_indices`方法。类似于迭代器的`enumerate`方法，会返回含有`(下标，值)`的元组。

2. `map.insert(k, v)`的返回值是一个`Option`。
    - 当`map`中没有`k`对应的`v`时，就返回`None`；
    - 当`map`中有`k`对应的`v`时，将此`v`包装成`some(v)`变体返回，然后此`v`再被`insert()`中的新`v`替代。

3. `if let`是模式匹配的一种语法糖，[rust圣经中的介绍](https://course.rs/basic/match-pattern/match-if-let.html#if-let-%E5%8C%B9%E9%85%8D)。

4. 我在滑动窗口解法中说："保留每一步的窗口长度，取最大值，就是此题答案"。其实是一种既浪费时间又浪费空间的思路。
    - 比较好的做法是在循环外定义一个变量`max`，每一次循环都用它和当前窗口长度比较，并保留最大值。