# 归并排序

```rust
// 注意区分 Vec<T> 和 [T]
pub fn merge_sort<T: Copy + Ord + Default>(arr: &mut [T]) {
    // 只有当元素个数大于一时才进行排序
    if arr.len() > 1 {
        let mid = arr.len() / 2;
        // 递归排序左边数组
        let mut left_arr = merge_sort(&mut arr[..mid]);
        // 递归排序右边数组
        let mut right_arr = merge_sort(&mut arr[mid..]);
        // 合并
        return merge(arr, mid);
    }
}

// 不能同时有两个可变引用，所以使用 arr, mid
fn merge<T: Copy + Ord + Default>(arr: &mut [T], mid: usize) {
    let mut left_arr = arr[..mid].to_vec();
    let mut right_arr = arr[mid..].to_vec();
    // 数组索引
    let (mut l, mut r) = (0, 0);
    for v in arr {
        // 这里的顺序不能交换。如果交换顺序，右侧全部 push 之后，r 会超过 right_arr.len，会导致越界错误
        if r == right_arr.len() || (l < left_arr.len() && left_arr[l] < right_arr[r]) {
            *v = left_arr[l];
            l += 1;
        } else {
            *v = right_arr[r];
            r += 1;
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn basic() {
        let mut res = vec![10, 8, 4, 3, 1, 9, 2, 7, 5, 6];
        merge_sort(&mut res);
        assert_eq!(res, vec![1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);
    }

    #[test]
    fn basic_string() {
        let mut res = vec!["a", "bb", "d", "cc"];
        merge_sort(&mut res);
        assert_eq!(res, vec!["a", "bb", "cc", "d"]);
    }

    #[test]
    fn empty() {
        let mut res = Vec::<u8>::new();
        merge_sort(&mut res);
        assert_eq!(res, vec![]);
    }

    #[test]
    fn one_element() {
        let mut res = vec![1];
        merge_sort(&mut res);
        assert_eq!(res, vec![1]);
    }

    #[test]
    fn pre_sorted() {
        let mut res = vec![1, 2, 3, 4];
        merge_sort(&mut res);
        assert_eq!(res, vec![1, 2, 3, 4]);
    }

    #[test]
    fn reverse_sorted() {
        let mut res = vec![4, 3, 2, 1];
        merge_sort(&mut res);
        assert_eq!(res, vec![1, 2, 3, 4]);
    }
}

```