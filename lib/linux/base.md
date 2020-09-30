# Linux基础

标签（空格分隔）： 前端知识体系

---

开源、免费的操作系统

## 发行版

Linux核心 + 自由软件 + 工具

| 命令 | 说明 |
| --- | --- |
| `uname -r` | 显示发行版本 |
| `uname -m` | 显示硬件架构，同arch |

### Red Hat类

如Red Hat、Centos、Fedora

* 软件包后缀名为.rpm
* 软件包安装工具为rpm
* 软件包依赖管理器为yum

#### rpm包

编译好的RPM程序

* Name：名称
* Version：版本号
* Release：发行商版本
* Architecture：架构
* Source RPM：源代码包
* Signature：签名

#### rpm

Red Hat Package Manager

| 命令 | 说明 |
| --- | --- |
| `rpm -q <package>` | query，查询已安装的rpm包 |
| `rpm -qi <package>` | info，列出详细信息 |
| `rpm -ql <package>` | list，列出包含的文件 |
| `rpm -qR <package>` | require，列出依赖的rpm包 |
| `rpm -qp <package>` | package，查询指定rpm包 |
| `rpm -qf <file>` | file，列出所属的rpm包 |
| `rpm -i <package>` | install，安装指定rpm包 |
| `rpm -iv <package>` | verbose，显示安装详细信息 |
| `rpm -ivh <package>` | 显示安装进度 |
| `rpm -ivh <package> --nodeps` | 不检查依赖 |
| `rpm -ivh <package> --noscripts` | 不执行scripts |
| `rpm -Uvh <package>` | 升级rpm包，未安装时直接安装 |
| `rpm -Fvh <package>` | 升级rpm包，未安装时不处理 |
| `rpm -e <package>` | 卸载rpm包 |
| `rpm --reinstall <package>` | 重新安装rpm包 |
| `rpm -V <package>` | 验证rpm包是否被修改 |

#### yum

Yellow dog Updater, modified，基于RPM的自动处理依赖关系的软件包管理器

添加阿里源

```bash
# 进入yum源存储路径
cd /etc/yum.repos.d

# 下载阿里源
wget -O /etc/yum.repos.d/Centos-7.repo http://mirrors.aliyun.com/repo/Centos-7.repo

# 清除并重新生成yum缓存
yum clean all
yum makecache
```

添加EPEL源

```
# 添加EPEL源
yum install epel-release

# 清除并重新生成yum缓存
yum clean all
yum makecache
```

命令

| 命令 | 说明 |
| --- | --- |
| `yum list <package>` | 列出包的安装情况 |
| `yum list installed` | 列出已安装包 |
| `yum info <package>` | 显示包信息 |
| `yum deplist <package>` | 显示包依赖信息 |
| `yum provides <package>` | 显示提供指定包的包 |
| `yum search <string>` | 搜索包 |
| `yum install <package>` | 安装包 |
| `yum install -y <package>` | 直接安装，不用回答 |
| `yum check` | 检查所有已安装包 |
| `yum check-update` | 检查更新 |
| `yum update <package>` | 更新包 |
| `yum remove <package>` | 删除包 |
| `yum reinstall <package>` | 重装包 |
| `yum clean all` | 清除缓存 |
| `yum makecache` | 生成缓存 |

### Debian类

如Debian, Ubuntu

* 软件包后缀名为.deb
* 软件包安装工具为dpkg
* 软件包依赖管理器为apt

#### dpkg

Debian Packager

| 命令 | 说明 |
| --- | --- |
| `dpkg -s <package>` | 查询包的安装情况 |
| `dpkg -l <package>` | 列出包信息 |
| `dpkg -L <package>` | 列出包包含的文件 |
| `dpkg -S <file>` | 列出文件所属的deb包 |
| `dpkg -i <package>` | 安装指定deb包 |
| `dpkg -r <package>` | 删除deb包 |

#### apt

Advanced Packaging Tool，基于dpkg的自动处理依赖关系的软件包管理器

| 命令 | 说明 |
| --- | --- |
| `apt update` | 获取最新的软件信息 |
| `apt list <package>` | 列出包的安装情况 |
| `apt list --installed` | 列出已安装包 |
| `apt show <package>` | 显示包信息 |
| `apt depends <package>` | 显示包依赖信息 |
| `apt rdepends <package>` | 显示依赖指定包的包 |
| `apt search <string>` | 搜索包 |
| `apt install <package>` | 安装包 |
| `apt check` | 检查所有已安装包 |
| `apt upgrade <package>` | 升级包 |
| `apt remove <package>` | 删除包 |
| `apt install <package> --reinstall` | 重装包 |

## 虚拟化

使用虚拟化技术生成操作系统环境

### Hypervisors

虚拟机监视器，如KVM、Xen、Hyper-V、VirtualBox

![25](虚拟化技术)

#### VirtualBox

能够创建、克隆、导入、导出操作系统环境

| 命令 | 说明 |
| --- | --- |
| `vboxmanage list vms` | 列出所有虚拟机 |
| `vboxmanage clonevm --register <vm> -n <newVm>` | 复制虚拟机 |
| `vboxmanage export <vm> -o <ovaFile>` | 导出虚拟机 |
| `vboxmanage import <ovaFile>` | 导入虚拟机 |

### Containers

容器管理器，如Docker、LXC

#### LXC

能够创建、启动、进入、关闭容器

| 命令 | 说明 |
| --- | --- |
| `lxc-ls --fancy` | 查看容器 |
| `lxc-create -n <container> -t <template>` | 创建容器 |
| `lxc-start -d -n <container>` | 后台启动容器 |
| `lxc-attach -n <container>` | 进入容器 |

## 用户命令

### id

查看用户和组信息

| 命令 | 说明 |
| --- | --- |
| `id` | 查看当前用户的用户信息和组信息 |
| `id <user>` | 查看指定用户的用户信息和组信息 |
| `id -u` | 查看用户ID |
| `id -g` | 查看组ID |

### su

切换用户

| 命令 | 说明 |
| --- | --- |
| `su` | 切换为root |
| `su <user>` | 切换为指定用户 |

### passwd

修改密码

| 命令 | 说明 |
| --- | --- |
| `passwd` | 修改当前用户的密码 |
| `passwd <user>` | 修改指定用户的密码 |

### sudo

以管理员权限运行

| 命令 | 说明 |
| --- | --- |
| `sudo <command>` | 以管理员权限运行命令 |
| `visudo` | vi /etc/sudoers，修改sudo配置 |

## 网络请求命令

### ifconfig

网卡配置

| 命令 | 说明 |
| --- | --- |
| `ifconfig` | 查看网卡配置 |
| `ifconfig -a` | 所有信息 |
| `ip addr` | 查看网卡信息，用于替代ifconfig |

### netstat

网络连接情况

| 命令 | 说明 |
| --- | --- |
| `netstat` | 显示tcp、udp、unix socket连接情况 |
| `netstat -a` | all，所有端口 |
| `netstat -t` | tcp，tcp端口 |
| `netstat -u` | udp，udp端口 |
| `netstat -l` | listen，正在监听的端口 |
| `netstat -p` | pid，显示进程 |
| `netstat -n` | number，显示ip地址 |
| `netstat -ap | grep <program>` | 查找进程 |
| `netstat -an | grep <:port>` | 查找端口 |

### ping

发送ICMP请求

| 命令 | 说明 |
| --- | --- |
| `ping <ip>` | 测试网络连接情况 |
| `ping <domain>` | 域名解析并测试连接请情况 |

### wget

下载文件

| 命令 | 说明 |
| --- | --- |
| `wget <url>` | web get，下载文件到当前目录 |
| `wget -O <file> <url>` | output-document，以指定文件名保存 |
| `wget -c <url>` | continue，断点续传 |
| `wget -b <url>` | background，后台下载 |
| `wget -t <tries> <url>` | tires，重试次数 |
| `wget -U <userAgent> <url>` | user agent，设置用户代理 |
| `wget --spider <url>` | 检测链接 |
| `wget -i <linkText> <url>` | input-file，读取文件中的链接进行下载 |
| `wget -m <url>` | mirror，下载整个网站到本地 |
| `wget -A <MIME> <url>` | accept，接受的文件格式 |
| `wget -R <MIME> <url>` | reject，拒绝的文件格式 |
| `wget --proxy=off <url>` | 不使用代理 |

### curl

发送请求

| 命令 | 说明 |
| --- | --- |
| `curl <url>` | client url，发送GET请求，输出响应体 |
| `curl -O <file> <url>` | output file，输出保存为文件 |
| `curl -X <action> <url>` | request，指定请求方法 |
| `curl -v <url>` | verbose，输出详细信息 |
| `curl -H <header> <url>` | header，设置header |
| `curl -A <url>` | user agent，设置用户代理 |
| `curl -e <referer> <url>` | referer，设置referer |
| `curl -b <cookie> <url>` | cookie，发送cookie |
| `curl -b <cookieTxt> <url>` | cookie，从文件中读取cookie发送 |
| `curl -c <cookieTxt> <url>` | 将服务器设置的cookie写入文件 |
| `curl -d <data> <url>` | data，发送POST请求，Content-Type为application/x-www-form-urlencoded |
| `curl -d <dataTxt> <url>` | 从文件中读取数据发送 |
| `curl --data-urlencode <data> <url>` | urlencode，对数据进行url编码 |
| `curl -G --data-urlencode <data>  <url>` | get，构成查询字符串 |
| `curl -F <formdata> <url>` | form，发送POST请求，Content-Type为multipart/form-data |
| `curl -I <url>` | head，发送head请求 |
| `curl -L <url>` | location，跟随重定向 |
| `curl -x <proxy> <url>` | 设置代理 |

### ssh

远程登录

| 命令 | 说明 |
| --- | --- |
| `ssh <user>@<host>` | secure shell，远程登录 |
| `ssh -p <port> <user>@<host>` | 指定ssh端口 |
| `ssh -i <privateKeyFile> <user>@<host>` | 指定私钥文件 |
| `ssh <user>@<host> <command>` | 在远程主机上执行命令 |
| `ssh-keygen` | 生成RSA密钥 |
| `ssh-keygen -t <type>` | type，指定密钥类型 |
| `ssh-keygen -b <bit>` | bit，指定密钥位数 |
| `ssh-keygen -f <file>` | file，指定密钥文件地址 |
| `ssh-keygen -C <comment>` | comment，指定密钥注释 |
| `ssh-copy-id <user>@<host>` | 上传公钥追加到远程主机的~/.ssh/authorized_keys文件中，相当于`cat ~/.ssh/id_rsa.pub | ssh <user>@<host> "cat >> ~/.ssh/authorized_keys"` |

## 系统信息命令

### systemctrl

系统服务管理

| 命令 | 说明 |
| --- | --- |
| `systemctrl` | 列出所有已启动的unit |
| `systemctrl start <service>` | 启动服务 |
| `systemctrl stop <service>` | 停止服务 |
| `systemctrl restart <service>` | 重启服务 |
| `systemctrl reload <service>` | 重新加载服务配置 |
| `systemctrl show <service>` | 显示服务配置 |
| `systemctrl enable <service>` | 允许开机自启动 |
| `systemctrl disable <service>` | 禁止开机自启动 |
| `systemctrl status <service>` | 查看服务状态 |
| `systemctrl list-dependencies <service>` | 查看服务依赖关系 |

### shutdown

关机

| 命令 | 说明 |
| --- | --- |
| `shutdown -k` | 发送关机警告信息 |
| `shutdown -h now` | 关机，相当于halt |
| `shutdown -r now` | 重启，相当于reboot、systemctrl reboot |

### ps

查看进程

| 命令 | 说明 |
| --- | --- |
| `ps -a` | process status，查看当前终端的进程信息 |
| `ps -e` | 查看所有进程信息 |
| `ps -f` | full format，全部列出 |
| `ps a` | 显示执行命令 |
| `ps f` | 显示树状结构 |
| `ps u` | 以用户为主 |
| `ps x` | 显示所有程序 |
| `ps -ef | grep <program>` | 搜索某个程序 |
| `ps aux | grep <program>` | 搜索某个程序 |

### lsof

查看进程打开的文件

| 命令 | 说明 |
| --- | --- |
| `lsof` | list opened files，查看所有打开的文件 |
| `lsof -c <process>` | 查看进程打开的文件 |
| `lsof -p <pid>` | 查看pid进程打开的文件 |
| `lsof <file>` | 查看打开文件的进程 |
| `lsof +d <dir>` | 查看打开目录中文件的进程 |
| `lsof -i [protocol][@hostname|ip][:port]` | 查看符合条件的进程，protocol为tcp或udp |

### journalctl

显示系统日志

| 命令 | 说明 |
| --- | --- |
| `journalctl` | 显示所有系统日志 |
| `journalctl | grep <string>` | 过滤系统日志 |
