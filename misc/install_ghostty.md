# 安装 xterm-ghostty

## 背景

在 macOS 上使用 Ghostty 终端进入虚拟机（OrbStack VM）或通过 SSH 登录远程主机时，执行 `clear` 或其他终端相关命令，报错：

``` shell
'xterm-ghostty': unknown terminal type
```

## 原因

Ghostty 会把 $TERM 设置为 `xterm-ghostty`.
而 Linux / Fedora / Orb VM 默认没有安装 Ghostty 的 terminfo，
`clear` 等命令依赖 termcap/terminfo 文件来识别终端类型，
如果 terminfo 缺失，就会报 `unknown terminal type`

## 解决方案

在 Mac 上，生成 `xterm-ghostty` 的 terminfo 文件：

``` shell
infocmp xterm-ghostty > xterm-ghostty.ti
```

将文件复制到 orb 虚拟机 / ssh 远程主机:


```shell
orb push xterm-ghostty.ti
```

```shell
scp xterm-ghostty.ti user@host
```

在 orb 虚拟机 / 远程主机安装 xterm-ghostty

```
tic -x xterm-ghostty.ti
```
