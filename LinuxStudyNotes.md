



# Linux 命令

[TOC]





## find

`find [搜索范围] [匹配条件]`

`find / -name 'home' 2>/dev/null` ，从根目录开始找名字为 **home** 的文件或目录。`-iname` 会 ignore case，即不匹配大小写。

`find / -name 'h???' 2>/dev/null` ，使用 shell 的通配符，即 `*` ，`?` ，`[]` 等。

find 的正则，`find / -regex ".*/H..." 2>/dev/null` ，用 `-regex` ，如果要选择正则引擎，用 `-regextype` 。注意：find 正则要匹配完整路径。如果只是 `H...` 将没用。

否定参数 ，查找所有非txt文本: `find . ! -name "*.txt" -print` 

 `find ./ -size +204800` ，查找目录下大于 100M 的文件，find 使用数据块为单位，2数据块==1K。`-size +n -n n`

`find ./ -maxdepth 1 -type f -user dq -exec ls -l {} \;` ，搜索深度为 1，即只打印当前目录，`-type f` 指定类型为文件，`-user` 指定文件所有者为 dq。然后将返回结果用 `ls -l` 显示。

`-maxdepth` 指定搜索深度,打印出当前目录的文件（深度为1）。

`-user` 和 `-group` 根据所有者和所属组查找。

`-type` 根据文件类型查找: **f 文件; d 目录; l 软链接文件**。

`-exec 命令 {} \;` or `| xargs 命令` ，注意：`{}` 与 `\` 之间有空格的，而 `\` 与 `;` 之间是没空格的。

根据时间查找：

- `-amin` 访问时间 access，单位是分钟。-atime 单位是天。-amin -5 表示5分钟内被访问的文件
- `-cmin` 文件属性 change, 例如: 位置, 所有者, 访问权限等
- `-mmin` 文件内容 modify

> -------------(+n)------------|----------------(n)-------------------|----------(-n)--------------
>       (n+1)\*24H前          |        (n+1)\*24H~n\*24H间    |         n\*24H内
>
> -ctime -n    查找距现在 n\*24H 内修改过的文件
> -ctime n    查找距现在 n\*24H 前, (n+1)\*24H 内修改过的文件
> -ctime +n    查找距现在 (n+1)\*24H 前修改过的文件



连接选项：

- `-a` 两个条件同时满足 and
- `-o` 两个条件满足一个即可 or

`-inum` 根据 i 节点查找.



`find / -size +204800 -a -mtime -1 2>/dev/null -exec ls -lh {} \;` 查找大于 100M 且在 24 小时内被修改过的文件。



> find 支持的文件检索类型可以区分普通文件和符号链接、目录等，即 `-type` 参数。但是二进制文件和文本文件无法直接通过find的类型区分出来；此时可以借助 `file` 命令查看文件具体类型。





## file

 `file` 命令可以检查文件具体类型（二进制或文本）。例如：

```shell
$ file 1.txt
1.txt: ASCII text, with CRLF line terminators
```



## grep

`grep [指定字串] [文件]`  这一点与 find 相反。

`ls --help | grep -e "-F"` 搜索 ls 中 `-F` 的说明，此时用到 `-e` 选项，其主要作用是用来搜索带连字符（`-`）的命令选项。 直接使用 `ls --help | grep "-F"` 是不行的。



```shell
dq@ubuntu GoProject -> grep -r --color=auto "package" ./
./src/github.com/HonestQiDong2/stringutil/reverse.go:package stringutil
./src/github.com/HonestQiDong2/hello/hello.go:package main
Binary file ./bin/hello matches
```

`-r`  `--recursive`  是为了在路径中查找，不然只能指定文件名。

`--color=auto`  是为了高亮找到的字串。

```shell
dq@ubuntu GoProject -> grep -Ir --color=auto "package" ./
./src/github.com/HonestQiDong2/stringutil/reverse.go:package stringutil
./src/github.com/HonestQiDong2/hello/hello.go:package main
```

`-I`  equivalent to `--binary-files=without-match` ，是为了排除二进制文件。

```shell
dq@ubuntu GoProject -> grep -nIr --color=auto "package" ./ 
./src/github.com/HonestQiDong2/stringutil/reverse.go:1:package stringutil
./src/github.com/HonestQiDong2/hello/hello.go:1:package main
```

`-n`  显示字串在所搜索文件中的位置，此例中字串 package 在 hello.go 和 reverse.go 的第一行。

```shell
dq@ubuntu GoProject -> grep -nIr --exclude-dir="hello" "package" ./ 
./src/github.com/HonestQiDong2/stringutil/reverse.go:1:package stringutil
```

`--exclude-dir` 搜索时排除目录，上例中排除 hello 目录下的文件。所以只显示 reverse.go 文件中的 package 字串。



`grep -v  "#" .bashrc`  显示 `.bashrc` 中的正文部分，即去掉注释。此时 `-v` 排除字串就有用了。



`-i`  忽略大小写的。





## sed

sed 可以有以下两种调用方式：

```shell
sed [-n] [-e] 'command(s)' files 
sed [-n] -f scriptfile files
```

`-e` 表示附加多条编辑命令。例如：执行两条编辑命令 `sed -e 's/MA/Andrew/' -e 's/PA/, Liu/' text.txt`

`-n` 取消默认输出，即不打印任何信息，配合 `s///p` 使用。例如：只打印第三行 `sed -n '3p' /etc/passwd` ；打印1，3行 `sed -n '1,3p' /etc/passwd` ；从第一行打印到匹配 fish 成功的那一行 `sed -n '1,/fish/p' test.txt`

`-r` 使用正则表达式。将 OK 变为 [OK]，两种方法等价：

- `sed 's/OK/[&]/g' test.txt`
- `sed -r 's/(OK)/[\1]/g' test.txt`

`-i` 直接修改文件内容，默认情况下 sed 只是打印修改过的文件内容



`s` 表示替换，`'s/MA/Andrew/'` 将 MA 替换成 Andrew。

`g` 表示对一行中的所有匹配进行操作，默认是只操作第一个匹配

- 只替换第3行的内容，用 '3s///'
- 替换3到6行的内容，用 '3,6s///'
- 只替换每一行第2次出现的匹配，用 's///2'
- 替换每一行第2次及之后的匹配，用 's///2g'

`p` 表示只输出被编辑的行，同时由于默认输出所有文本，所以需要配合 `-n` 选项使用。

`d` 删除行，不指定行号则删除所有行

- 删除第一行 `sed '1d' text.txt`
- 删除最后一行, `$` 表示最后一行 `sed '$d' text.txt`
- 删除包含 MA 的行 `sed '/MA/d' text.txt`
- 删除包含 MA 的行及其后的2行，`sed '/MA/,+2d' test.txt`

`&` 表示被匹配的变量

- 将 OK 变为 [OK]，`sed 's/OK/[&]/g' test.txt`。而正则中（-r 选项）则使用 `\1, \2` 匹配

`a` 在匹配行的下一行插入文本，用 \ 和 空格效果一样

- `sed '/John/a\I love zhx' test.txt`
- `sed '/John/c I love zhx' test.txt`
- `sed '1 a hello' test.txt` 在第一行的下一行（第二行）插入字串 hello

`i` 在匹配行的上一行插入文本，类似 `a`

`c` 替换匹配行，类似 `a`





## awk

awk 的主要功能是针对档案的每一行(line)搜寻指定的 patterns。当一行里有符合指定的 patterns，awk 就会在此一行执行被指定的 actions。 awk 依此方式处理输入档案的每一行直到输入档案结束。

 	awk 程式是由很多的 **pattern** 与 **action** 所组成，action 写在大括号 { } 里面，一个 pattern 后面就跟著一个action。整个 awk 程式会像下面的样子：`pattern {action}` 。

 	在 awk 程式里面的规则，pattern 或 action 能够被省略，但是两个不能同时被省略。如果 pattern 被省略，对于输入档里面的每一行，action 都会被执行。如果 action 被省略，内定的 action 则会印出所有符合 pattern 的输入行。

 	awk 的输入单位叫 “记录” (records)，默认情况下一行为一个记录，一个记录又可以被分为多个字段 (field)，默认字段间以空格或者制表符分隔。

 	记录与记录间以 **RS** (record separator) 隔开，RS 是一个字符串，内定值为'\n'， 仅有 RS 中的第一个字元是有效的，其他字元会被忽略。**NR** 存储当前已读取多少行, print NR, $0 输出行号和每行内容。

 	内建变数 **FS** (field separator) 将一个记录分解为多个字段。默认为空格或者 TAB。`$0` 表示所有字段，`$1` 表示第一个被分割的字段。`-F ':'` 等价于 `-F:`， 指定多个 FS 使用 `-F '[;:]'`。内建变数 **NF** 表示目前这个记录中的字段个数，所以 $NF 表示最后一个字段。

 	在 awk 程式里，可以使用 = 来改变 FS 的值。例如：

```shell
echo 'John Q. Smith, 29 Oak St., Walamazoo, MI 42139' | awk 'BEGIN {FS=","}; {print $2}'
```

打印的就是 ` 29 Oak St.` ，即以逗号 ',' 为分割。与使用 `-F ","` 一样的功能。

 	**ORS** (output record separator) 定义 print 打印每个记录时的分隔。**OFS** (output field separator) 定义 print 打印多个项时的分隔。

 	printf 不会自动换行, 内建变数 OFS 和 ORS 对 printf 也没有任何影响。格式化输出：

```shell
cat /etc/passwd | awk -F ':' '{printf "%s ---- %s\n", $1, $2}'
```



测试文件 BBS-list

```
aardvark 555-5553 1200/300 B
alpo-net 555-3412 2400/1200/300 A
barfly 555-7685 1200/300 A
bites 555-1675 2400/1200/300 A
camelot 555-0542 300 C
core 555-2912 1200/300 C
fooey 555-1234 2400/1200/300 B
foot 555-6699 1200/300 B
macfoo 555-6480 1200/300 A
sdace 555-3430 2400/1200/300 A
sabafoo 555-2127 1200/300 C
```

`awk -F ' ' '/foo/ {print $0}' BBS-list`  搜寻输入档里的每一行是否含有子字串 'foo'，如果含有 'foo' 则执行 action。action 为 print $0，是将现在这一行的内容印出。结果如下： 

```
fooey 555-1234 2400/1200/300 B
foot 555-6699 1200/300 B
macfoo 555-6480 1200/300 A
sabafoo 555-2127 1200/300 C
```

`awk '$1~/foo/ {print $0}' BBS-list`  是对每个记录的第一个栏位做检查，如果它包含子字串 'foo'，则打印该记录。结果如下： 

```
fooey 555-1234 2400/1200/300 B
foot 555-6699 1200/300 B
macfoo 555-6480 1200/300 A
sabafoo 555-2127 1200/300 C
```

`awk 'BEGIN {OFS=";"; ORS="\n\n"} {print $1, $2}' BBS-list`  打印出的结果如下：

```
aardvark;555-5553

alpo-net;555-3412

barfly;555-7685

bites;555-1675

...
```





## xsel

windows 下有 clip，linux 下是 xsel，mac 下是 pbcopy。

```shell
$ echo "hello" | xsel -b
# 将 hello 复制到剪切板

$ xsel -b < 1.txt
# 将 1.txt 文件的内容复制到剪切板

$ xsel -b -c 
# 清空剪切板

$ echo | xsel -b
# 将一个空行（\n）复制到剪切板
```

> `-b` 表示在 clipboard 上操作



参考文献：

http://oldratlee.com/post/2012-12-23/command-output-to-clip





## Bash 快捷键

`Ctrl + a` - Jump to the start of the line

`Ctrl + b` - Move back a char

`Ctrl + c` - Terminate the command

`Ctrl + d` - Delete from under the cursor

`Ctrl + e` - Jump to the end of the line

`Ctrl + f` - Move forward a char

`Ctrl + k` - Delete to EOL

`Ctrl + l` - Clear the screen  

`Ctrl + r` - Search the history backwards 

`Ctrl + R` - Search the history backwards with multi occurrence

`Ctrl + u` - Delete backward from cursor

`Ctrl + xx` - Move between EOL and current cursor position

`Ctrl + x @` - Show possible hostname completions 

`Ctrl + z` - Suspend/ Stop the command

`Ctrl + h` - backspace，删除光标前边的字符

`Ctrl + w` - 删除当前光标到前边的最近一个空格之间的字符



## which 

查看文件安装路径



## whereis

查看程序搜索路径

> 当安装同一软件多个版本时特别有用



## info

查看命令的详细介绍，最好用 `info ls | less` 这样查看比较方便。



## vi

`:r` 导入其他文件到当前 vi 打开的文件中

`:r !date` 将当前时间日期时间导入到文件中

快捷键定义，例如：`map ^P I#` 注释一行；`map ^B 0x` 取消一行的注释;**注意：^ 是通过 ctrl+v+p 打印出来的**。

`ab` 替换，例如：`ab mymail abc@163.com` 在插入模式下，输入 mymail 再按空格或回车，此时 mymail 变成[abc@163.com](mailto:abc@163.com)

配置文件，在用户家目录下创建 `.vimrc` 文件. 例如：`vi /home/dq/.vimrc` `set number` 此时用 vi 打开文件时都会显示行号了



## history

- `!n` 重复执行第 n 条历史命令
- `!!` 重复执行上一条历史命令
- `!字符串` 重复执行最后一条以该字符串开头的命令





## tar

`tar -C /usr/local -xzf go1.7.4.linux-amd64.tar.gz`  将 golang 的安装包解压到 /usr/local 路径下。

`-C, --directory=DIR`  change to directory DIR



## ls

`ls -R`  会显示子目录。`-R, --recursive`  list subdirectories recursively

`-d`  显示目录本身的信息，而不是目录下包含的信息

`-i`  显示 i 节点

`-F`  会在文件夹后加 `/` ，在软链接后加 `@` 。

`-h`  显示文件大小时更加人性化。



## pwd

`-P` 显示出实际目录，而非链接目录



## help

`help cd` 查看 shell 内置命令，因为 `cd --help` 无效。



## tail

`-f` output appended data as the file grows



## cp

`i`  interactive， 遇到同名文件时，会有 prompt。

`-n, --no-clobber`  不会覆盖一个已存在的文件。设置 `-n` 之后 `-i` 将失效。

`-r` 复制文件夹。



## cat

`ls | cat -n` 为每项文件前加编号。



## chmod

- `chmod [{ugoa}{+-=}{rwx}][文件或目录][mode=421][文件或目录]`
- `-R` 递归修改权限

| 代表字符 | 对文件      | 对目录           |
| ---- | -------- | ------------- |
| r    | 可以查看文件内容 | 可以列出目录中的内容    |
| w    | 可以修改文件内容 | 可以在目录中创建或删除文件 |
| x    | 可以执行文件   | 可以进入目录        |



## locate

在文件资料库中搜索

`updatedb` 更新文件资料库，在使用 `locate` 就可以查找最新的文件了

`/tmp` 下的内容没有收藏在 **mlocate.db** 中，所以其中的文件用 locate 找不到的



## rm

删除当前目录下所有文件有两种方法：

1. `find -exec rm -fr {} \;` or `find | xargs rm -rf`
2. `rm -fr *`

但这两种方法存在一个重要的差异，即对 `.bashrc` 这类以 `.` 开头的文件，**方法1**会删除，而**方法2**不会删除。



## less

`/keywords` 反白显示 `keywords`，按 `n` 显示下一个查找到的位置，`N` 显示上一个

`-N` 显示行号，**奇怪的是 `-n` 不能显示行号**。









# Linux 问题

## sudo 提示密码反应慢

一般是由于/etc/hosts文件中没有对应的 ”主机名 <--> IP地址"  相互解析条目

解决办法：

1.  命令 `hostname` 查看主机名，例如主机名为 ubuntu。
2.  修改 /etc/hosts 文件，添加一行 `127.0.0.1      ubuntu` 。



## sudo su 之后关闭 terminal 

直接关闭会有如下提示：**There is still a process running in this terminal. Closing the terminal will kill it.**

解决办法：在 root 用户中，`exit` 或 `ctrl + d` 退出，然后会转到普通用户，再 `exit` 或 `ctrl + d` 即可正常退出。



参考文献：

https://bbs.archlinux.org/viewtopic.php?id=132138



## Vim 代码缩进设置

```shell
set number
set ts=4
set sw=4
set expandtab
set softtabstop=4
```



参考文献：

http://linux-wiki.cn/wiki/zh-hans/Vim%E4%BB%A3%E7%A0%81%E7%BC%A9%E8%BF%9B%E8%AE%BE%E7%BD%AE





## 历史命令

`!!`  上一条命令。

`!-n`  向后选择 n 条命令，`!-n` 与 `!!` 等价。

`!string`  选择最近以 string 开头的命令。

`!?string?`  选择最近包含 string 的命令。

`!!:n`  获取上一条命令的第 n 个参数。

`!!:^`  上一个命令的第一个参数，等同于 `!^`。`!!:$`  上一个命令的最后一个参数，等同于 `!$`。

`!string:n` 获取最近执行过的 string 开头的命令的第 n 个参数。