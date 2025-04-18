# 使用 EasyTier 异地组网

[EasyTier](https://easytier.cn/) 是一个由 Rust 和 Tokio 驱动的异地组网方案。

使用 EasyTier 将阿里云服务器、主机和树莓派组成虚拟局域网，方便在任何地点访问校园网的设备。

## 公网服务器

EasyTier 提供了共享的公网节点，可以实现无公网 IP 组网。
这里还是使用自己的公网服务器。

由于网络问题，可以预先从 [Release](https://github.com/EasyTier/EasyTier/releases) 下载好预编译二进制文件，上传到服务器，并解压安装：

``` shell
unzip easytile*.zip && rm easytier*.zip
mv easytier*/* /usr/bin

easytier-core --version
```

添加 `/etc/systemd/system/easytier.service` 文件，编写 easytier.service，保持 easytier 持续运行：

``` service
[Unit]
Description=EasyTier Core Service
After=network.target

[Service]
ExecStart=/usr/bin/easytier-core --ipv4 10.144.144.1
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

`--ipv4` 参数指定当前节点的在虚拟局域网的 IP 地址

启动服务：

``` shell
systemctl enable --now easytier

systemctl status easytier
```

## 局域网树莓派

局域网中的树莓派已经预先安装了 rust 工具链，可以使用 `cargo install` 本地编译安装。

``` shell
cargo install easytier
```

同样，编写 easytier.service:

``` service
[Unit]
Description=EasyTier Core Service
After=network.target

[Service]
ExecStart=/usr/bin/easytier-core --ipv4 10.144.144.2 --peers udp://<ip>:11010
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

`--peers`: 服务器的公网 IP

如果无法联通，检查公网服务器是否开放 `11010` 端口

## 主机

可以在 [Release](https://github.com/EasyTier/EasyTier/releases) 下载 gui 版安装包，安装运行。

![capture](https://doc.oee.icu:60009/server/index.php?s=/api/attachment/visitFile&sign=40c36755e534247f6a02343aeb1a899f)
