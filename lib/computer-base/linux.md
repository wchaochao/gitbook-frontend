# Linux基础

标签（空格分隔）： 前端知识体系

---

## 介绍

开源、免费的操作系统

### 发行版

Linux核心 + 自由软件 + 工具

* 使用RPM方式安装软件的系统：Red Hat, Fedora, SuSE等
* 使用dpkg方式安装软件的系统：Debian, Ubuntu, B2D等

### 文件系统

层级机构

| 目录 | 说明 |
| --- | --- |
| / | Root，根目录 |
| /etc | program Configuration files，程序配置文件 |
| /var | Frequently changing content，经常变动的内容，如日志文件 |
| /home | User account files，用户文件 |
| /sbin | System binary files，系统二进制文件 |
| /bin | User binary files，用户二进制文件 |
| /lib | Share libraries，共享库 |
| /usr | Third-party binaries，第三方二进制文件 |

## shell

与操作系统沟通的壳

### 命令

```
$ <command> [option]... [arg]...
```

* command: 命令或可执行文件，为命令时去$PATH中查找命令对应的可执行文件
* option: 命令选项，分短选项和长选项两种，可多个
* arg: 命令参数，可多个
* 空格: 分隔命令、选项、参数
* \Enter: 换行
* Enter: 执行命令

### 快捷键

* Tab: 自动补全
* Ctrl + A: 跳转到命令开头
* Ctrl + E: 跳转到命令结尾
* Ctrl + U: 删除光标到命令开头的字符
* Ctrl + K: 删除光标到命令结尾的字符
* Ctrl + C: 中断命令
* Ctrl + D: 退出
* Up：上一个命令
* Down：下一个命令
* Shift + PageUp: 向前翻页
* Shift + PageDown: 向后翻页

### 路径

* `.`: 当前目录
* `..`: 父目录
* `-`: 上一个工作目录
* `~`: 当前使用者的主目录
* `~<account>`: account的主目录

### 帮助

| 命令 | 说明 |
| --- | --- |
| command --help | 显示命令的帮助信息 |
| man command | 显示命令的使用手册 |
| info command | 显示命令的info文档 |

## 导航命令

### ls

展示目录内容

| 命令 | 说明 |
| --- | --- |
| ls | list，展示当前目录内容 |
| `ls <dir>` | list directory，展示指定目录内容 |
| ls -l | long，长列表展示 |
| ls -lh | human-readable，文件尺寸以人类可读的形式展示 |
| ls -a | all，展示所有信息，包括隐藏文件 |

### pwd

显示工作目录

| 命令 | 说明 |
| --- | --- |
| pwd | present work directory，显示工作目录 |

### cd

修改工作目录

| 命令 | 说明 |
| --- | --- |
| `cd <dir> ` | change directory，修改工作目录 |
