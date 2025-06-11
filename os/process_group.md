# Process Group

在符合 POSIX 标准的操作系统中，**process group** 表示一个和多个进程组成的集合。

进程组用于 signal 的分发，当 signal 被发送到一个进程组时，会传递给进程组中的每个进程。

类似地，session 是进程组的集合，一个进程不能创建属于另一个会话的进程组，也不能加入另一个会话的进程组。即，进程不能从一个会话迁移到另一个会话。

进程组的概念独立于进程树，可以是同一个进程的父子，也可以是兄弟进程，甚至没有直接的亲缘关系。

![2 level hierachy of process](https://biriukov.dev/docs/fd-pipe-session-terminal/images/process-group-session.png)

❶ – 会话 ID（SID）与会话领导者进程（bash）相同 PID 。

❷ – 会话领导者进程（bash）拥有自己的进程组，其中它是领导者，因此 PGID 与其 PID. 相同。

❸, ❹ – 该会话有 2 个额外的进程组，ID 分别为 200 和 300。

❺, ❻ – 只有一个进程组可以是终端的前台。所有其他进程组都是后台。

❼, ❽, ❾ – 所有会话的成员共享伪终端 /dev/pts/0 。

## 应用

通过进程组分发信号，是 Shell 程序作业控制（Job Control）的基础。例如，当键盘按下 `Ctrl` + `C` 时，前台进程组的每一个进程都会收到 `SIGINT`，结束进程。

*这个过程涉及 Shell、TTY 设备驱动、OS input 子系统 和 OS 进程管理子系统，详见 [Shell、TTY 和 Kernel](/os/shell_tty_kernel.md)*

---

**Read More**： [Shell、TTY 和 Kernel](/os/shell_tty_kernel.md)

**Reference**

[Process group - Wiki](https://en.wikipedia.org/wiki/Process_group)

[Process groups, jobs and sessions | Viacheslav Biriukov](https://biriukov.dev/docs/fd-pipe-session-terminal/3-process-groups-jobs-and-sessions/)