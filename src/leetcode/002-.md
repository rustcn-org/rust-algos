# 2. 两数相加
题目链接：[两数相加](https://leetcode.cn/problems/add-two-numbers/)

这题有两种解法，一种是常规的循环模拟，另一种是递归。个人认为递归的解法更简洁。

## 递归解法
首先，这题为什么可以用递归？

- 如题，`结果链表相应节点的数字 = 所给链表的对位节点和的个位数 + 进位;`
    - 这等于直接给出了递推公式
- 递归函数交付给下层什么？
    - 两节点的之和的进位
- 递归函数的终止条件是什么？
    - 当两个所给链表都为None，且当前进位为0时

```rust
use leetcode_rust::ListNode;

impl Solution {
    pub fn add_two_numbers(
        l1: Option<Box<ListNode>>,
        l2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        carried(l1, l2, 0)
    }
}

pub fn carried(
    l1: Option<Box<ListNode>>,
    l2: Option<Box<ListNode>>,
    mut carry: i32, // carry代表进位，其初始值只有0或1
) -> Option<Box<ListNode>> {
    if l1.is_none() && l2.is_none() && carry == 0 {
        None
    } else {
        Some(Box::new(ListNode {
            next: carried(
                l1.and_then(|x| {
                    carry += x.val;
                    x.next
                }),
                l2.and_then(|x| {
                    carry += x.val;
                    x.next
                }),
                carry / 10,
            ),
            val: carry % 10,
        }))
    }
}
```

对于Rust新手(编者本人🤷‍♂️)，我在解题时遇到的问题。

1. `Option<Box<ListNode>>`是链表的常规写法。
2. 对Option的and_then方法不熟悉。可以简单的把它理解成：只有Option所包含的值不为None时才执行的map方法。
3. 对于递归函数carried，在使用时并不是看到carried()就马上开始调用，而是要先算出这个函数的参数，在此题中就先进行闭包运算，之后在进行下一场递归。所以闭包体中的carry变量只能在本层捕获，再通过carried()的第三个参数传道下一层。

## 模拟解法
[常规模拟](https://leetcode.cn/problems/add-two-numbers/solution/liang-shu-xiang-jia-by-leetcode-solution/)