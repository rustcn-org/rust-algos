# 5. 最长回文子串
题目链接：[最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

这题比较容易想到的一个解法是中心扩散法。

利用反证法：既然题目要我们找回文子串，那找到的回文子串一定有一个(回文子串的长度为奇数)或两个(回文子串的长度为偶数)*中心*，从这个*中心*开始，左右两边的字符互为镜像。

## 中心扩散解法
我们可以用穷举法找出最长回文子串的*中心*，也就是假设每一个字符都可能是回文子串的*中心*，在找到其中最长的那个。

```rust
use std::iter::FromIterator;

impl Solution { // 中心扩散法
    pub fn longest_palindrome(s: String) -> String {
        let n = s.len();
        let s: Vec<char> = s.chars().collect(); // 注意需要收集器显式的指定一个结果集，这里用的是Vec<char>
        let mut start = 0;
        let mut end = 0;
        for i in 0..n {
            let mut l = i; // 扩散窗口的左边界
            let mut r = i; // 扩散窗口的右边界
            while r + 1 < n && s[r + 1] == s[l] { // 中心也可是两个相同的字符
                r += 1;
            }
            while l > 0 && r < n - 1 && s[l - 1] == s[r + 1] {
                l -= 1;
                r += 1;
            }
            if end - start < r - l {
                start = l;
                end = r;
            }
        }
        String::from_iter(&s[start..=end])
    }
}
```

使用rust解此题时需要注意的地方：

1. rust不支持直接对`String`类型进行索引，[rust圣经中的解释](https://course.rs/basic/compound-type/string-slice.html#%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%B4%A2%E5%BC%95)。所以要先将`String`类型转换为`Vec<char>`。并且为了方便，变量`s`使用了变量遮蔽(shadowing)。

2. 题目要求返回`String`类型，所以还要将`Vec<char>`再转成`String`。这就用到了`String::from_iter()`方法，此方法并不在标准库内，需要使用`use std::iter::FromIterator;`进行导入。