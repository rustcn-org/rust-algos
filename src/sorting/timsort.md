# Timsort

> 算法由 [xieyu567](https://github.com/xieyu567) 提交

```rust,editable
use std::cmp::min;

pub fn find_min_run(mut n: usize) -> usize {
    let mut r = 0;
    let minimum = 64;
    while n >= minimum {
        r |= n & 1;
        n >>= 1;
    }
    n + r
}

pub fn insert_sort<T>(arr: &mut [T], left: usize, right: usize)
    where T: PartialOrd + Copy, {
    for i in left + 1..right + 1 {
        let element = arr[i];
        let mut j = i - 1;
        while element < arr[j] && j >= left {
            arr[j + 1] = arr[j];
            j -= 1;
        }
        arr[j + 1] = element;
    }
}

pub fn merge<T>(arr: &mut [T], left: usize, mid: usize, right: usize)
    where T: PartialOrd + Copy, {
    let array_length1 = mid - left + 1;
    let array_length2 = right - mid;
    let left_arr = Vec::from(&arr[left..left + array_length1]);
    let right_arr = Vec::from(&arr[mid + 1..mid + 1 + array_length2]);

    let (mut i, mut j, mut k) = (0, 0, left);
    while j < array_length2 && i < array_length1 {
        if left_arr[i] <= right_arr[j] {
            arr[k] = left_arr[i];
            i += 1;
        } else {
            arr[k] = right_arr[j];
            j += 1
        }
        k += 1
    }
    while i < array_length1 {
        arr[k] = left_arr[i];
        k += 1;
        i += 1
    }
    while j < array_length2 {
        arr[k] = right_arr[j];
        k += 1;
        j += 1
    }
}

pub fn tim_sort<T>(arr: &mut [T])
    where T: PartialOrd + Copy, {
    let n = arr.len();
    let min_run = find_min_run(n);

    for start in (0..n).filter(|x| x % min_run == 0) {
        let end = min(start + min_run - 1, n - 1);
        insert_sort(arr, start, end)
    }
    let mut size = min_run;
    while size < n {
        for left in (0..n).filter(|x| x % (2 * size) == 0) {
            let mid = min(n - 1, left + size - 1);
            let right = min(left + 2 * size - 1, n - 1);
            merge(arr, left, mid, right)
        }
        size = 2 * size
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn cal_min() {
        let n = 189;
        let min_run = find_min_run(n);
        assert_eq!(48, min_run);
        assert_eq!(61, find_min_run(976));
    }

    #[test]
    fn insert_test() {
        let mut vec = vec![7, 49, 73, 58, 30, 72, 44, 78, 23, 9];
        insert_sort(&mut vec, 0, 9);
        assert_eq!(vec![7, 9, 23, 30, 44, 49, 58, 72, 73, 78], vec)
    }

    #[test]
    fn tim_test() {
        let mut vec = vec![7, 49, 73, 58, 30, 72, 44, 78, 23, 9];
        tim_sort(&mut vec);
        assert_eq!(vec, vec![7, 9, 23, 30, 44, 49, 58, 72, 73, 78]);
    }
}

```