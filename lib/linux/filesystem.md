# 文件系统

标签（空格分隔）： 前端知识体系

---

## 目录结果

| 目录 | 说明 |
| --- | --- |
| / | Root，根目录 |
| /etc/ | program Configuration files，程序配置文件 |
| /var/ | Frequently changing content，经常变动的内容，如日志文件 |
| /home/ | User account files，用户文件 |
| /sbin/ | System binary files，系统二进制文件 |
| /bin/ | User binary files，用户二进制文件 |
| /lib/ | Share libraries，共享库 |
| /usr/ | unix software resource，软件资源 |
| /dev/ | 设备文件 |
| /sys/ | 虚拟文件系统，记录系统核心信息 |

### /etc

| 目录 | 说明 |
| --- | --- |
| /etc/passwd | 用户信息 |
| /etc/shadow | 用户密码信息 |
| /etc/group | 用户组信息 |
| /etc/gshadow | 用户组密码信息 |
| /etc/sudoers | sudo配置 |
| /etc/sudoers.d | sudo配置附属目录 |
| /etc/profile | 系统配置 |
| /etc/profile.d | 系统配置附属目录 |
| /etc/bashrc | bash配置 |
| /etc/inputrc | 全局的快捷键绑定 |
| /etc/shells | 可使用的shells |
| /etc/hosts | hosts配置 |
| /etc/init.d/ | 系统初始化 |
| /etc/ssh/ | ssh服务信息 |

### /usr

| 目录 | 说明 |
| --- | --- |
| /usr/sbin | 管理员常用指令集 |
| /usr/bin | 一般指令集 |
| /usr/lib | 共享库 |
| /usr/local | 本地安装的软件 |

### /dev

| 目录 | 说明 |
| --- | --- |
| /dev/stdin | 标准输入文件 |
| /dev/stdout | 标准输出文件 |
| /dev/stderr | 标准错误文件 |
| /dev/null | 空设备文件 |
| /dev/sda | SCSI Device A |
| /dev/hda | Hard Drive A |
| /dev/vda | Virtl Drive A |
| /dev/sr0 | DVD Drive 0 |
| /dev/fd0 | Floppy Drive 0 |
| /dev/cdrom | CD-ROM Drive |

### /sys

| 目录 | 说明 |
| --- | --- |
| /sys/block/ | 系统存储的block情况 |
| /proc/self/fd/ | 文件描述符情况 |

### ~

当前用户信息

| 目录 | 说明 |
| --- | --- |
| ~/.bash_profile | 当前用户配置 |
| ~/.bash_login | .bash_profile的替代文件 |
| ~/.profile | .bash_login的替代文件 |
| ~/.bashrc | 当前用户的bash配置，可配置PATH、alias |
| ~/.bash_logout | 当前用户登出时的操作 |
| ~/.bash_history | 当前用户的操作历史 |
| ~/.inputrc | 当前用户的快捷键绑定 |
| ~/.ssh | ssh相关文件 |

## 目录命令

### ls

输出目录内容

| 命令 | 说明 |
| --- | --- |
| `ls` | list，输出当前目录内容 |
| `ls <dir>` | list directory，输出指定目录内容 |
| `ls -l` | long，长列表展示 |
| `ls -lh` | human-readable，文件尺寸以人类可读的形式展示 |
| `ls -a` | all，展示所有信息，包括隐藏文件 |
| `ls -i` | inode，显示inode |
| `ls -F` | classify，显示文件类型符号 |

### pwd

显示工作目录

| 命令 | 说明 |
| --- | --- |
| `pwd` | present working directory，显示工作目录 |

### cd

修改工作目录

| 命令 | 说明 |
| --- | --- |
| `cd` | change directory，修改工作目录为主目录 |
| `cd -` | 同cd |
| `cd <dir> ` | change directory，修改工作目录为指定目录 |

### mkdir

创建目录

| 命令 | 说明 |
| --- | --- |
| `mkdir <dir>` | make directory，创建目录 |
| `mkdir -p <dir>` | parents，父目录不存在时创建父目录 |

### rmdir

删除空目录

| 命令 | 说明 |
| --- | --- |
| `rmdir <dir>` | remove directory，删除空目录 |
| `rmdir -p <dir>` | parents，删除空目录及其父空目录 |

## 文件命令

### touch

更新文件的访问和修改时间，不存在时创建

| 命令 | 说明 |
| --- | --- |
| `touch <newfile>` | 创建文件 |
| `touch <existfile>` | 更新文件的访问和修改时间为当前时间 |

### stat

查看文件属性

| 命令 | 说明 |
| --- | --- |
| `stat <file>` | 查看指定文件属性 |

### cp

复制文件或目录

| 命令 | 说明 |
| --- | --- |
| `cp <sourcefile> <destfile>` | 复制文件到当前目录 |
| `cp <sourcefile> <destdir>` | 复制文件到指定目录 |
| `cp -r <sourcedir> <destdir>` | 复制目录到指定目录 |

### mv

移动文件或目录

| 命令 | 说明 |
| --- | --- |
| `mv <sourcefile> <destfile>` | 重命名文件 |
| `mv <sourcefile> <destdir>` | 移动文件到指定目录 |
| `mv -r <sourcedir> <destdir>` | 移动目录到指定目录 |

### rm

删除文件或目录

| 命令 | 说明 |
| --- | --- |
| `rm <file>` | 删除文件 |
| `rm -r <dir>` | 删除目录 |
| `rm -rf <source>` | 强制删除 |

### scp

远程复制文件

| 命令 | 说明 |
| --- | --- |
| `scp <localFile> <user>@<host>:<dir>` | secury copy，复制本地文件到远程指定目录 |
| `scp -r <localDir> <user>@<host>:<dir>` | 复制本地目录到远程指定目录 |
| `scp <user>@<host>:<file> <localDir>` | 复制远程文件到本地指定目录 |
| `scp -r <user>@<host>:<dir> <localDir>` | 复制远程文件到本地指定目录 |
| `scp -P <port> <localFile> <user>@<host>:<dir>` | 指定远程主机端口号 |

## 文件内容命令

### cat

拼接指定文件并输出

| 命令 | 说明 |
| --- | --- |
| `cat` | concatenate，输出标准输入内容 |
| `cat -` | 同cat |
| `cat <file> ` | concatenate file，输出文件内容 |
| `cat -n <file> ` | number，列出行号 |

### less

分页查看文件内容

| 命令 | 说明 |
| --- | --- |
| `less <file>` | 分页查看文件内容 |


* h: 帮助
* q: 退出
* Space: 向下翻页
* PageDown: 向下翻页
* PageUp: 向上翻页
* g: 跳到第一行
* G: 跳到最后一行
* `/<string>`: 向下搜索字符串
* `?<string>`: 向上搜索字符串
* n: 下一个搜索
* N: 上一个搜索

### shasum

生成、检测消息摘要

| 命令 | 说明 |
| --- | --- |
| `shasum <file>` | 使用sha1算法生成文件的消息摘要 |
| `shasum -a <sha> <file>` | algorithm，指定sha算法 |

## 编辑器命令

### nano

使用nano编辑器创建或打开文件

| 命令 | 说明 |
| --- | --- |
| `nano <file>` | 使用nano编辑器创建或打开文件 |


* `Ctrl + G`: 获取帮助
* `Ctrl + X`: 离开
* `Ctrl + O`: 保存
* `Ctrl + W`: 搜索

### vi/vim

使用vi/vim编辑器创建或打开文件

| 命令 | 说明 |
| --- | --- |
| `vi/vim <file>` | 使用vi/vim编辑器创建或打开文件 |


* `i`: 进入编辑模式
* `:q`: 离开
* `:w`: 保存
* `:wq!`: 强制保存并离开

## 文件查找命令

### which

在$PATH中查找命令对应的可执行文件

| 命令 | 说明 |
| --- | --- |
| `which <command>` | 在$PATH中查找命令对应的可执行文件 |

### whereis

在特定目录下搜索文件或目录

| 命令 | 说明 |
| --- | --- |
| `whereis -l` | 列出查找的特定目录 |
| `whereis <file>` | 在特定目录下搜索文件 |
