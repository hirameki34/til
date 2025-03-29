# 理解 `as_ref`

`AsRef::as_ref()` 一般用在泛型代码中，添加 `AsRef` 约束。
一般不应该在泛型代码之外使用 `AsRef::as_ref`。如果想得到 `a` 的引用，请使用 `&a`。

除了 `AsRef` trait 之外，一部分 struct 也定义了 `as_ref()` 方法，并不是为了实现 `AsRef` trait。
例如，`Option::as_ref()` 将 `&Option<T>` 转换为 `Option<&T>`。

``` rust
impl<T> Option{
    ...
    pub const fn as_ref(&self) -> Option<&T> {
        match *self {
            Some(ref x) => Some(x),
            None => None,
        }
    }
    ...
}
```

`AsRef::as_ref()` 将一个类型的引用转换为另一个类型的引用
（更确切地说，它将一个值的引用转换为另一个类型的值的引用），
而其他则是从引用（`&Option<T>`）创建一个所有权的值（`Option<&T>`）。

关于引用，以下是三件不同的事：

1. `&x` 将值转换为对该值的引用。
2. 对实现了 `AsRef<T>` 的类型 `U` 调用 `as_ref()`，将执行从 `&U` 到 `&T` 的低成本转换。
3. 对 `&Option<T>` / `&Result<T, E>` 调用 `.as_ref()`，将它们转换为拥有 Option<&T> 或 Result<&T, &E> 的所有者。

---

**Reference** [What is the difference between as_ref() & '&'? - help - The Rust Programming Language Forum](https://users.rust-lang.org/t/what-is-the-difference-between-as-ref/76059)
