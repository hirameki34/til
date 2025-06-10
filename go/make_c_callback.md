# 使用 cgo 调用 C callback

go 中无法直接调用 C 函数指针，需要通过 C 语言间接调用。

包含 `//export` 的文件中 preamble 不能包含函数定义，需要写在另一文件中。

## demo

``` go
// api.go

// typedef void (*callback_t)(int);
// void bridge(int n, callback_t callback)
import "C"

//export call()
func call(n C.int, cb C.callback) {
    C.bridge(n, cb);
}
```

```go
// callback.go

// typedef void (*callback_t)(int);
// extern void bridge(int n, callback_t callback) {
//     callback(n);
// }
import "C"
```

**Reference**: [Go calls C pointer](https://github.com/enobufs/go-calls-c-pointer)