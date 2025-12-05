# 使用 Bear 配置 C 项目在 VSCode / clangd 上的语言服务

## 问题背景

在 C 项目中使用 VSCode 或 clangd 进行开发时，
如果项目不是 CMake 或 Makefile 过于复杂，
clangd 无法自动识别编译选项、宏定义和头文件路径，导致：

* 全部报错（红色提示）
* 自动补全和跳转定义失效

解决方法是生成 **编译数据库 (`compile_commands.json`)**，让 clangd 知道每个文件的编译命令。

## Bear 工具简介

* **作用**：监听 `make` 执行过程，生成 `compile_commands.json`
* **安装**（Fedora/Ubuntu/Debian）：

```bash
sudo dnf install bear         # Fedora
sudo apt install bear          # Debian/Ubuntu
```

* **生成编译数据库**：

```bash
bear -- make <args> 
```

* 生成文件：

```
./compile_commands.json
```

该文件包含每个源文件的编译命令、头文件路径、宏定义等。

