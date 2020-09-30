# Bash脚本

标签（空格分隔）： 前端知识体系

---

包含一系列命令的文本文件

## 执行

| 命令 | 说明 |
| --- | --- |
| `bash <scriptpath>` | 执行脚本文件 |
| `<scriptpath>` | 脚本文件有可执行文件 |
| `<scriptfile>` | 脚本文件$PATH中 |

## Shebang行

指定解释器

```bash
#!/usr/bin/env bash
```

## 注释

```bash
# comment
```

## 脚本参数

脚本文件名后面可以带有参数

### 引用

* `$0`：脚本文件路径
* `$1~$9`：对应第一个到第九个参数
* `${N}`：对应第九个后的参数
* `$#`：参数总数
* `$@`：全部参数，使用空格分隔
* `$*`：全部参数，使用`$IFS`空格分隔

### shift

移除参数

| 语法 | 说明 |
| --- | --- |
| `shift` | 移除第一个参数 |
| `shift <N>` | 移除前N个参数 |

### getopts

解析命令短参数

```bash
getopts optstring name
```

* 参数开头不为连字符，退出解析，`$OPTIND`为该参数是第几个参数
* 参数开头为连字符，对连字符后的字符挨个解析
 * 字符不在optstring中，name为?
 * 字符在optstring中，name为该字符，optstring中该字符后有冒号时，该命令短参数后带有参数，值存储在$OPTARG中

```bash
while getopts 'lha:' OPTION; do
  case "$OPTION" in
    l)
      echo "linuxconfig"
      ;;

    h)
      echo "h stands for h"
      ;;

    a)
      avalue="$OPTARG"
      echo "The value provided is $OPTARG"
      ;;
    ?)
      echo "script usage: $(basename $0) [-l] [-h] [-a somevalue]" >&2
      exit 1
      ;;
  esac
done
shift "$(($OPTIND - 1))"
```

## 读取输入

从标准输入中读取一整行分隔成参数存储到变量中

```bash
read [-options] [variable...]
```

* 参数按环境变量`$IFS`分隔，与变量一一对应
 * 没有变量，整行参数存储到环境变量`$REPLY`中
 * 变量比参数少，最后一个变量存储剩下的所有参数
 * 变量比参数多，剩余变量为空值

| 命令 | 说明 |
| --- | --- |
| `read -n <number>` | number，读取指定个字符 |
| `read -a` | array，读取为数组 |
| `read -p` | prompt，提示语 |
| `read -t <timeout>` | timeout，超时时间 |
| `read -s` | 输入不显示在终端，用于输入密码 |
| `read -r` | raw，不把反斜杠解释为转义 |
| `read -d <delimiter>` | 设置读取结束符 |
| `read -e` | 允许使用readline快捷键 |
| `IFS="seperator" read` | 临时设置IFS |

## 条件判断

### if结构

* 命令执行成功，执行if分支
* 命令执行失败，执行elif或else分支

```bash
if commands; then
  commands
[elif commands; then
  commands...]
[else
  commands]
fi
```

### test命令

表达式判断

| 命令 | 说明 |
| --- | --- |
| `test <expression>` | expression为真时命令执行成功 |
| `[ <expression> ]` | 相当于test，[]与表达式直接要有空格 |
| `[[ <expression> ]]` | 支持正则 |
| `[ <expression1> && <experssion2> ]` | 与 |
| `[ <expression1> || <experssion2> ]` | 或 |
| `[ ! \( <expression> \) ]` | 非 |

### 文件判断

文件变量需放入双引号中，防止变量不存在时判断为真

| 命令 | 说明 |
| --- | --- |
| `[ -a <file> ]` | 文件是否存在 |
| `[ -e <file> ]` | exist，文件是否存在 |
| `[ -f <file> ]` | file，是否是普通文件 |
| `[ -d <file> ]` | directory，是否是目录 |
| `[ -b <file> ]` | block，是否是块设备文件 |
| `[ -c <file> ]` | char，是否是字符设备文件 |
| `[ -L <file> ]` | link，是否是符号链接文件 |
| `[ -p <file> ]` | pipe，是否是管道文件 |
| `[ -S <file> ]` | socket，是否是Socket文件 |
| `[ -u <file> ]` | user，文件是否设置了setuid |
| `[ -g <file> ]` | group，文件是否设置了组ID |
| `[ -r <file> ]` | read，文件是否可读 |
| `[ -w <file> ]` | write，文件是否可写 |
| `[ -x <file> ]` | execute，文件是否可执行 |
| `[ -s <file> ]` | 文件长度不为0 |
| `[ <file1> -nt <file2> ]` | 文件1比文件2更新 |
| `[ <file1> -ot <file2> ]` | 文件1比文件2更旧 |
| `[ <file1> -ef <file2> ]` | 文件1和文件2引用相同的inode |

### 字符串判断

字符串变量需放入双引号中，防止变量不存在时判断为真

| 命令 | 说明 |
| --- | --- |
| `[ <string> ]` | 字符串不为空 |
| `[ -n <string> ]` | 字符串不为空 |
| `[ -z <string> ]` | 字符串为空 |
| `[ <string1> = <string2> ]` | 字符串相同 |
| `[ <string1> != <string2> ]` | 字符串不相同 |
| `[ <string1> '>' <string2> ]` | 字符串大于 |
| `[ <string1> '<' <string2> ]` | 字符串小于 |

### 整数判断

| 命令 | 说明 |
| --- | --- |
| `[ <integer1> -eq <integer2> ]` | 整数相等 |
| `[ <integer1> -ne <integer2> ]` | 整数不相等 |
| `[ <integer1> -lt <integer2> ]` | 整数小于 |
| `[ <integer1> -le <integer2> ]` | 整数小于或等于 |
| `[ <integer1> -gt <integer2> ]` | 整数大于 |
| `[ <integer1> -ge <integer2> ]` | 整数大于或等于 |

### 算术判断

| 命令 | 说明 |
| --- | --- |
| `((expression))` | 结果非0为真 |

### 正则判断

| 命令 | 说明 |
| --- | --- |
| `[ <string1> =~ <regex> ]` | 正则匹配 |

### case结构

多值判断

* expression为表达式
* pattern为值或通配符模式，以`;;`结尾（匹配结束）或`;;&`结尾（可以继续向下匹配）
 * `a)`：匹配a
 * `a|b)`：匹配a或b
 * `?)`：匹配单个字符
 * `*)`：匹配任意个字符
 * `[a-z])`：匹配a-z
 * `[[:class:]])`：匹配字符类

```bash
case expression in
  pattern )
    commands ;;
  pattern )
    commands ;;
  ...
esac
```

## 循环

### while循环

* 符合条件时，执行命令，并再次判断条件
* 不符合条件时，退出命令

```bash
while condition; do
  commands
done
```

### until循环

* 不符合条件时，执行命令，并再次判断条件
* 符合条件时，退出命令

```bash
until condition; do
  commands
done
```

相当于

```bash
while ! condition; do
  commands
done
```

### for循环

```bash
for (( expression1; expression2; expression3 )); do
  commands
done
```

相当于

```bash
((expression1))
while ((expression2)); do
  commands
  ((expression3))
done
```

### for/in循环

遍历列表中的每一项

* list可以是空格分隔的词，通配符匹配的文件，子命令的输出
* in list省略时为全部参数

```bash
for variable in list; do
  commands
done
```

### break/continue

* break：跳出循环，不再执行
* continue：终止本轮循环，执行下一轮循环

### select结构

生成菜单供用户选择

* 生成菜单，内容为编号加list的每一项
* 用户输入编号，该项的内容存入variable中，编号存入环境变量REPLY中
* 执行commands，回到第一步继续选择
* 用户输入Enter时，回到第一步，输入Ctrl+C退出

```bash
select variable in list; do
  commands
done
```

## 函数

封装重复使用的代码片段

### 声明

* 函数体内可以使用参数变量引用函数参数，同脚本参数
* 函数体内直接声明的变量为全局变量，使用local声明局部变量
* 使用return返回值

```bash
fn () {
  # code
}

function fn {
  # code
}
```

### 调用

```bash
fn [arg...]
```

### 删除

| 命令 | 说明 |
| --- | --- |
| `unset -f <fn>` | 删除函数 |

## 数组

包含多个成员的变量，编号从0开始

### 创建

| 命令 | 说明 |
| --- | --- |
| `<array>=(<value>...)` | 创建数组并按顺序给成员赋值 |
| `<array>=(<index>=<value>...)` | 创建数组并按指定的索引给成员赋值 |
| `<array><index>=<value>` | 创建数组并给成员赋值 |
| `declare -a <array>` | 声明数组 |
| `read -a <array>` | 读取输入为数组 |

### 读取

| 命令 | 说明 |
| --- | --- |
| `echo "${array[@]}"` | 读取所有成员为列表 |
| `echo "${array[*]}"` | 读取所有成员为单个字符串 |
| `echo ${array[i]}` | 读取索引为i的成员 |
| `echo $array` | 读取索引为0的成员 |

### 长度

| 命令 | 说明 |
| --- | --- |
| `${#array[@]}` | 数组长度，成员的个数 |
| `${#array[*]}` | 同上 |

### 序号

| 命令 | 说明 |
| --- | --- |
| `${!array[@]}` | 数组序号，返回所有成员的序号 |
| `${!array[*]}` | 同上 |

### 成员

| 命令 | 说明 |
| --- | --- |
| `array[i]=value` | 设置成员 |
| `array=value` | 设置索引为0的成员 |
| `unset array[i]` | 删除成员 |
| `unset array` | 删除所有成员 |
| `${array[@]:position:length}` | 提取从position开始的length个成员，length不存在时到底 |
| `array+=(value...)` | 追加成员 |

## 临时文件

安全创建临时文件

* 创建前检查临时文件是否存在
* 确保临时文件创建成功
* 临时文件要有权限限制
* 临时文件要使用不可预测的文件名
* 脚本退出时，要删除临时文件

```
trap 'rm -f "$TEMPFILE"' EXIT

TMPFILE=$(mktemp) || exit 1
echo "Our temp file is $TMPFILE"
```

### mktemp

创建临时文件

| 命令 | 说明 |
| --- | --- |
| `mktemp` | 创建临时文件 |
| `mktemp -d` | 创建临时目录 |
| `mktemp -u` | 只输出文件名，不创建 |
| `mktemp -p <dir>` | 指定临时文件的父目录，默认为/tmp |
| `mktemp -t <template>` | 设置文件名模板，默认为tmp.XXXXXXXXXX |

### trap

响应系统信号

| 命令 | 说明 |
| --- | --- |
| `trap -l` | 列出所有系统信号 |
| `trap '<command/fn>' EXIT` | 编号0，脚本退出时执行命令 |
| `trap '<command/fn>' INT` | 编号2，用户按下Ctrl+C，意图中断脚本 |
| `trap '<command/fn>' QUIT` | 编号3，用户按下Ctrl+/，意图退出脚本 |
| `trap '<command/fn>' KILL` | 编号9，用户杀死进程 |

## 除错

考虑命令会出错的情况

```bash
[[ -d $dir_name ]] && cd $dir_name && echo rm *
```

运行参数

```bash
set -euxo pipefail
```

环境变量

* `LINENO`：当前行号
* `FUNCTION`：当前函数的调用堆栈，索引0对应当前函数
* `BASH_SOURCE`：当前脚本的调用堆栈，索引0对应当前脚本
* `BASH_LINENO`：当前函数的调用行号堆栈
