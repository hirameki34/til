# SSH 配置快捷登录

SSH 可以通过配置文件指定 host、用户、端口等信息，从而实现快速登录。

SSH 配置文件 `~/.ssh/config`，可以指定

| 配置项          | 作用                                |
| ------------ | --------------------------------- |
| Host         | SSH 命令别名，例如 `pi`                  |
| HostName     | 远程主机地址，IP 或 mDNS 名称，例如 `pi.local` |
| User         | 登录用户名，例如 `pi` 或 `u`               |
| IdentityFile | 使用的私钥文件                           |

例如：

``` text
Host pi
    HostName pi.local
    User pi
```
