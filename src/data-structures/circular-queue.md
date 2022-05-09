# 循环队列(Circular Queue)

循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”

循环队列的一个好处是我们可以利用这个队列之前用过的空间。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。

__Properties__
* de_queue O(1)
* en_queue O(1)

```rust
use std::{
    alloc::{self, Layout},
    ptr,
};

pub struct CircularQueue<T> {
    write_index: usize,
    read_index: usize,
    cap: usize,
    len: usize,
    data: Box<[T]>,
}

impl<T> CircularQueue<T> {
    pub fn new(cap: usize) -> Self {
        let data = unsafe {
            let data_ptr = alloc::alloc(Layout::array::<T>(cap).unwrap()) as *mut T;
            Box::from_raw(ptr::slice_from_raw_parts_mut(data_ptr, cap))
        };
        Self {
            write_index: 0,
            read_index: 0,
            cap,
            len: 0,
            data,
        }
    }

    /**
     * add value to CircularQueue
     * return true if success, false if queue is full
     */
    pub fn en_queue(&mut self, value: T) -> bool {
        if self.is_full() {
            false
        } else {
            self.data[self.write_index] = value;
            self.write_index = (self.write_index + 1) % self.cap;
            self.len += 1;
            true
        }
    }

    /**
     * remove value from CircularQueue
     * return true if success, false if queue is full
     */
    pub fn de_queue(&mut self) -> bool {
        if self.is_empty() {
            false
        } else {
            self.read_index = (self.read_index + 1) % self.cap;
            self.len -= 1;
            true
        }
    }

    /**
     * get front value
     */
    pub fn front(&self) -> Option<&T> {
        if self.is_empty() {
            None
        } else {
            Some(&self.data[self.read_index])
        }
    }

    /**
     * get tail value
     */
    pub fn rear(&self) -> Option<&T> {
        if self.is_empty() {
            None
        } else if self.write_index == 0 {
            Some(&self.data[self.cap - 1])
        } else {
            Some(&self.data[self.write_index - 1])
        }
    }

    pub fn is_empty(&self) -> bool {
        self.len == 0
    }

    pub fn is_full(&self) -> bool {
        self.cap == self.len
    }
}

#[cfg(test)]
mod tests {
    use std::result;

    use super::*;

    #[test]
    fn test_en_queue() {
        let mut circular_queue = CircularQueue::new(100);
        for i in 0..100 {
            let result = circular_queue.en_queue(i);
            assert!(result);
        }
        let result = circular_queue.en_queue(100);
        assert!(!result);
    }

    #[test]
    fn test_de_queue() {
        let mut circular_queue = CircularQueue::new(100);
        for i in 0..200 {
            circular_queue.en_queue(i);
        }

        for _ in 0..100 {
            let result = circular_queue.de_queue();
            assert!(result);
        }

        let result = circular_queue.de_queue();
        assert!(!result);
    }

    #[test]
    fn test_circular_queue_order() {
        let mut circular_queue = CircularQueue::new(3);

        for i in 1..4 {
            circular_queue.en_queue(i);
        }

        circular_queue.en_queue(4);

        let result = circular_queue.rear();
        assert_eq!(result, Some(&3));

        assert!(circular_queue.is_full());

        circular_queue.de_queue();

        circular_queue.en_queue(4);
        let result = circular_queue.front();
        assert_eq!(result, Some(&2));

        let result = circular_queue.rear();
        assert_eq!(result, Some(&4))
    }
}
```