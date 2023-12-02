# 在 Bash 中设置命令超时

`timeout` 命令可以在指定时间后对执行的命令发送 `SIGTERM` 信号，终止执行。

```bash
$ timeout 1s bash -c 'for((;;)); do :; done'
$ echo $?
124
```

`echo $?` 打印出 `124`，表示进程因默认的 `SIGTERM` 停止。

使用 `--preserve-status` 选项可以保留进程的返回值。

```bash
$ timeout --preserve-status 1s bash -c 'exit 66'
$ echo $?
66
```

**Reference**: [Setting Command Timeouts in Bash](https://www.baeldung.com/linux/bash-timeouts)
