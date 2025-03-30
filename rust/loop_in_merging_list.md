# 合并链表时的环从何而来？

## 背景

在练习 Rust 关于合并链表的题目时，遇到了一个很奇怪的现象。

链表定义：

``` rust
struct Node<T> {
    val: T,
    next: Option<NonNull<Node<T>>>,
}

struct LinkedList<T> {
    length: u32,
    start: Option<NonNull<Node<T>>>,
    end: Option<NonNull<Node<T>>>,
}
```

想通过修改 `next` 指针的方式实现归并排序，利用引用进行了如下实现：

``` rust
pub fn merge(list_a: LinkedList<T>, list_b: LinkedList<T>) -> Self {
    let start;
    let mut tail;

    let mut a = &list_a.start;
    let mut b = &list_b.start;

    // 找到新链表的头
    unsafe {
        if a.unwrap().as_ref().val < b.unwrap().as_ref().val {
            start = a;
            tail = a;
            a = &(a.unwrap().as_ref()).next;
        } else {
            start = b;
            tail = b;
            b = &(b.unwrap().as_ref()).next;
        }
    }

    while a.is_some() && b.is_some() {
        let va = unsafe { &(a.unwrap().as_ref()).val };
        let vb = unsafe { &(b.unwrap().as_ref()).val };
        // 依次拼接后续节点
        if va < vb {
            unsafe {
                tail.unwrap().as_mut().next = *a;
                // 移动指针 a
                a = &(a.unwrap().as_ref()).next;
            }
        } else {
            unsafe {
                tail.unwrap().as_mut().next = *b;
                // 移动指针 b
                b = &(b.unwrap().as_ref()).next;
            }
        }

        unsafe {
            // 移动指针 tail
            tail = &tail.unwrap().as_ref().next;
        }
    }

    // 省略剩余节点处理过程...

    Self {
        length: list_a.length + list_b.length,
        start: *start,
        end: *tail,
    }
}
```

然而，这种实现会卡在 `while` 循环。
在 `while` 中加入 `assert`，发现以下断言会不成立：

``` rust
assert_ne!(tail, a);
assert_ne!(tail, b);
```

也就是说，即使执行了 `tail = &tail.unwrap().as_ref().next`，
`tail` 仍会和 `a` 或 `b` 相等。指向同一地址。
之后，在修改 `tail.next` 时，就会出现环，导致死循环。

## 探究

为了探究这个问题，我通过一个最小的 demo 还原了这个现象：

首先，准备环境，创建两条单链表。

``` rust
// Prepare two linked-lists,
//   a_start: &Option<NotNull<Node>> -> list_a
//   b_start: &Option<NotNull<Node>> -> list_b
//   tail -> list_b
let a = Box::new(Node::new(0));
let a = &Some(unsafe { NonNull::new_unchecked(Box::into_raw(a)) });
let mut first = Box::new(Node::new(1));
first.add(2);
let b_start = Some(unsafe { NonNull::new_unchecked(Box::into_raw(first)) });
let mut b = &b_start;
let tail = b;
```

- `a`: 指向链表 A 的第一个节点。
- `b`: 指向链表 B 的第一个节点。
- `tail`: 赋值为 `b`，指向 list B 的第一个节点。

然后，移动 b，赋值为 `&b.next`。记录此时 `next` 的地址。

```rust
// Move b to next
//   b -> first.next
b = unsafe { &b.unwrap().as_ref().next };
let t_next_addr = unsafe { format!("{:p}", &tail.unwrap().as_mut().next) };
let b_next_addr = unsafe { format!("{:p}", &b.unwrap().as_mut().next) };
```

之后，将 `t.next` 赋值为 `*a`，再次记录 `next` 的地址。

``` rust
unsafe {
    // Change tail to a_start
    tail.unwrap().as_mut().next = *a;
}
let t_next_addr_new = unsafe { format!("{:p}", &tail.unwrap().as_mut().next) };
let b_next_addr_new = unsafe { format!("{:p}", &b.unwrap().as_mut().next) };
```

最后，比较 `next` 地址，发现了这个现象：

``` rust
assert_ne!(b_next_addr, b_next_addr_new);
assert_eq!(t_next_addr, t_next_addr_new);
assert_eq!(b_next_addr_new, a_next_addr);
```

也就说：**修改 `t.next`，没有影响 `t.next` 的地址，反而影响了 `b.next` 的地址。**
而这个地址**变成了 `a.next` 的地址**。

## 分析

这个问题的核心在于，节点 `next` 字段类型是 `Option<...>`，是一个具有所有权的**值**。
使用 `&` 借用时，实际上是获取了**这个值**的地址。

``` rust
next: Option<NonNull<Node<T>>>,
```

即，`&node.next` 返回了 `node` 结构体中的 `next` 字段在堆上的地址，而不是 `next` 所指向的下一个节点在堆上的地址。

这类似一个二级指针，类似 `**Node<T>`，当对 `&node.next` 解引用时，得到的是一个 `Option` 对这个 `Option` 中的值再解引用，才能到达下一个节点。

因此，回到之前的代码：

``` rust
let b_start = Some(unsafe { NonNull::new_unchecked(Box::into_raw(first)) });
let mut b = &b_start;
let tail = b;
```

`b` 引用了 `b_start` 这个 `Option`，`tail` 赋值为 `b`，也引用了这个 `Option`.

```rust
b = unsafe { &b.unwrap().as_ref().next };
```

现在，`b` 引用了 `b.next` 这个 `Option`。即 `b` 的值是 `b.next` 在堆上的地址。

至此，一个奇怪的结构形成了:

- `tail->next` 是一个值，这个值存储在堆上，是指向链表第二个节点的指针。
- 同时，`b` 指向（借用）了这个值。

```rust
tail.unwrap().as_mut().next = *a;
```

当修改 `tail->next` 时，就修改了 `b` 指向的值。
此时，发生了数据竞争，违反了 rust 的所有权和借用规则。发生了 Undefined Behavior。

因此：

1. **修改 `t.next`，没有影响 `t.next` 的地址**: 这是正常现象，`t.next` 在内存中的位置没有变化。因为 `t` 始终是第一个节点。
2. **影响了 `b.next` 的地址。**：由于 `b` 指向的内存区域的数据发生了变化，因此 `b.next` 也发生了变化。`t.next` 改为 `a`，`b` 也指向了 `a`。

再回到最开始的链表合并的问题。

循环中的操作会使，`b` 或 `a` 在循环中会指向对方，在通过 `tail` 修改 `next` 字段，就会出现环，导致无限循环。

## 结论

在 `unsafe` 中一定要注意数据竞争问题。

事实上，此处无需引入额外的多级引用，只需创建 `Option<NotNull<Node>>` 类型的指针，作为`a`、`b` 或 `tail`。
Rust 有很多智能指针类型，在智能指针类型上添加引用，要思考是否一定要这么做。

---

``` rust
pub fn merge(mut list_a: LinkedList<T>, mut list_b: LinkedList<T>) -> Self {
    // 处理空链表的情况
    if list_a.start.is_none() {
        return list_b;
    }
    if list_b.start.is_none() {
        return list_a;
    }

    // 确定合并后链表的起点
    let mut merged_list = LinkedList::new();
    merged_list.length = list_a.length + list_b.length;
    
    // 获取链表的起始指针
    let mut a_current = list_a.start;
    let mut b_current = list_b.start;
    
    // 确定起始节点
    unsafe {
        if a_current.unwrap().as_ref().val <= b_current.unwrap().as_ref().val {
            merged_list.start = a_current;
            a_current = a_current.unwrap().as_ref().next;
        } else {
            merged_list.start = b_current;
            b_current = b_current.unwrap().as_ref().next;
        }
    }
    
    // 获取当前尾部节点的可变引用
    let mut current = merged_list.start;
    
    // 开始合并
    while a_current.is_some() && b_current.is_some() {
        unsafe {
            if a_current.unwrap().as_ref().val <= b_current.unwrap().as_ref().val {
                // 修改 current.next 指向 a_current
                (*current.unwrap().as_ptr()).next = a_current;
                // 更新 current
                current = a_current;
                // 移动 a_current 到下一个节点
                a_current = a_current.unwrap().as_ref().next;
            } else {
                // 修改 current.next 指向 b_current
                (*current.unwrap().as_ptr()).next = b_current;
                // 更新 current
                current = b_current;
                // 移动 b_current 到下一个节点
                b_current = b_current.unwrap().as_ref().next;
            }
        }
    }
    
    // 处理剩余节点
    unsafe {
        if a_current.is_some() {
            (*current.unwrap().as_ptr()).next = a_current;
            merged_list.end = list_a.end;
        } else if b_current.is_some() {
            (*current.unwrap().as_ptr()).next = b_current;
            merged_list.end = list_b.end;
        } else {
            // 如果两个链表都已经遍历完，则当前节点就是尾部节点
            merged_list.end = current;
        }
    }
    
    // 防止 list_a 和 list_b 的析构函数释放节点内存
    list_a.start = None;
    list_a.end = None;
    list_b.start = None;
    list_b.end = None;
    
    merged_list
}
```
