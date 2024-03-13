# 处理 OS signal

go 中，可以通过 `channel` 的形式处理信号

``` go
// 1. 创建 os.Signal 类型的 channel
ch := make(chan os.Signal, 1)
// 2. 订阅信号
signal.Notify(ch)
// OR: signal.Notify(ch, syscall.SIGINT,...)
// 3. 接收信号，执行处理逻辑
s := <-ch
```

**Reference**: [Go语言之信号处理](https://zhuanlan.zhihu.com/p/128953024)
