[TOC]



# git 命令



## git remote

`git remote -v` 显示远程仓库及其 URL。

`git remote add <shortname> <url>` 添加新的远程仓库。

`git remote show [remote-name]` 显示远程仓库的更多信息。

`git remote rename` 和 `git remote rm` 是远程仓库的移除与重命名。





## git push



`git push origin --delete <branchName>`，删除远程分支 

`git push origin --delete tag <tagname>`，删除远程标签



## git clone

`git clone -o custom_name` 使用自定义的名字作为远程仓库名

`--origin <name>` `-o <name>` Instead of using the remote name `origin` to keep track of the upstream repository, use `<name>`.





## git show

git show 查看提交时默认是输出 diff 信息的，会很占版面，如果要取消 diff 信息，用 `git show -s` 。

`-p` `-u` `--patch` Generate patch (see section on generating patches).

`-s` `--no-patch`  Suppress diff output. Useful for commands like `git show` that show the patch by default, or to cancel the effect of `--patch`.



## git branch

`-v` 

`-vv` 还显示远程分支信息

`--verbose` 

When in list mode, show sha1 and commit subject line for each head, along with relationship to upstream branch (if any). **If given twice, print the name of the upstream branch**, as well (see also git remote show <remote>).

`-a` 可以查看所有分支，包括 **fetch** 下来的远程分支

> 注意，`-vv` 会列出跟踪分支与远程分支关于提交的更新情况，但是这些数据都不是实时最新的，它来自于你从每个服务器上最后一次抓取的数据，如果想要统计最新的领先和落后数据，可以用 `git fetch --all; git branch -vv` 。



`git branch --merged/--no-merged` 显示已合并到当前分支或还未合并到当前分支的分支名。

`git branch -d` 删除分支，如果它包含一些还未合并的工作会删除失败，但可以用 `-D` 强制删除。

`git branch -d -r origin/master` 删除远程跟踪分支，用 `-d -r` 选项。

`-r` `--remotes` List or delete (if used with -d) the remote-tracking branches.



设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支，你可以在任意时间使用 `-u` 或 `--set-upstream-to` 选项运行 `git branch` 来显式地设置。命令： `git branch -u origin/serverfix` 。

`-u <upstream>` 设置跟踪分支

`--set-upstream-to=<upstream>` 

Set up **branchname's** tracking information so upstream is considered **branchname's** upstream branch. If no **branchname** is specified, then it defaults to the current branch.





## git clean

`-n` `--dry-run` Don’t actually remove anything, just show what would be done.

`-f` `--force` If the Git configuration variable clean.requireForce is not set to false, *git clean* will refuse to delete files or directories unless given -f, -n or -i. Git will refuse to delete directories with .git sub directory or file unless a second -f is given.

`-x` 小写的 x。Don’t use the standard ignore rules read from .gitignore (per directory) and $GIT_DIR/info/exclude, but do still use the ignore rules given with -e options. This allows removing all untracked files, including build products. This can be used (possibly in conjunction with git reset) to create a pristine working directory to test a clean build.

`-X` 大写的 x。Remove only files ignored by Git. This may be useful to rebuild everything from scratch, but keep manually created files.

`-d` Remove untracked directories in addition to untracked files.

>   git clearn 默认是不清理 **.gitignore** 中的文件的，如果要清除忽略的文件，用 `-X` ，如果要清除所有未跟踪的文件，用 `-x`。





## git stash



`git stash` or `git stash save` 储藏修改，并将新的储藏推送到栈上。`--keep-index` 告诉 Git 不要储藏任何你通过 git add 命令已暂存的东西。**如果指定 `--include-untracked` 或 `-u` 标记，Git 也会储藏任何未跟踪的文件**。

>   注意，默认情况下 stash 只会储藏跟踪了的文件。

`--include-untracked` 或 `-u`  不会储藏 .gitignore 忽略的文件。而 `--all` 除了储存未跟踪的文件，还会储存忽略的文件。等于是 `--all` 储存的范围比 `--include-untracked` 要大。

`git stash --patch`  

`git stash list` 查看存储在栈上的东西。

`git stash apply` or `git stash apply stash@{0}` 只能恢复工作区，但如果需要把暂存区也按照储藏时的暂存区恢复的话，要用 `--index` 。

`git stash drop` 删除，`git stash clear` 清空





## git tag

`git tag -l 'v1.8.5*'` 列出 1.8.5 系列的标签。列出如下标签。

```s
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5.1
v1.8.5.2
v1.8.5.3
```

`-l <pattern>` `--list <pattern>` List tags with names that match the given pattern (or all if no pattern is given). Running "git tag" without arguments also lists all tags. **The pattern is a shell wildcard** (i.e., matched using fnmatch(3)). Multiple patterns may be given; if any of them matches, the tag is shown. 注意：pattern 中可以用通配符。

Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）。

一个轻量标签很像一个不会改变的分支 - 它只是一个特定提交的引用。然而，附注标签是存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。 通常建议创建附注标签，这样你可以拥有以上所有信息；但是如果你只是想用一个临时的标签，或者因为某些原因不想要保存那些信息，轻量标签也是可用的。

附注标签创建：`git tag -a v1.2 -m "my version 1.2 [commit]"` 

轻量标签创建：`git tag v1.2-lw [commit]` 

`git show` 可以看到标签信息与对应的提交信息。

默认情况下，`git push` 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。 这个过程就像共享远程分支一样 - 你可以运行 `git push origin [tagname]`。`git push origin --tags` 一次性推送所有标签。





## git reset







## git checkout

`git checkout -- file` 是从当前**暂存区**中恢复**工作区**中的 file 文件。而 `git checkout HEAD -- file` 是从当前的**版本库**中**恢复工作区和暂存区**的 file 文件。

`git checkout 6e49dd2 -- "*"` 从提交 6e49dd2 中恢复工作区和暂存区中的所有文件。



**reset 和 checkout 对树的影响图** 

|                            | HEAD | Index | Workdir | WD Safe? |
| -------------------------- | ---- | ----- | ------- | -------- |
| **Commit Level**           |      |       |         |          |
| `reset --soft [commit]`    | REF  | NO    | NO      | YES      |
| `reset [commit]`           | REF  | YES   | NO      | YES      |
| `reset --hard [commit]`    | REF  | YES   | YES     | **NO**   |
| `checkout [commit]`        | HEAD | YES   | YES     | YES      |
| **File Level**             |      |       |         |          |
| `reset (commit) [file]`    | NO   | YES   | NO      | YES      |
| `checkout (commit) [file]` | NO   | YES   | YES     | **NO**   |

> WD Safe: 工作区（Working Directory）是否安全



`git checkout -b [branch] [remotename]/[branch]` 设置远程仓库上新的跟踪分支。如果不用设置新的分支名，可以用 `git checkout --track origin/serverfix` 这条命令会创建一个新的分支 serverfix，即新分支名与远程仓库上的分支名一样。





## git merge



`--no-ff` 强制创建 merge commit，即使可以 fast-farward merge。这样在 master 分支上可以有一个新的节点，保证版本演进的清晰。

`git merge --abort` 中断 merge。



如果两个分支没有共同的祖先，例如一个本地分支与一个远程分支合并。此时需要使用 `--allow-unrelated-histories` 选项。



merge 时如果有二进制文件发生冲突，则使用如下的命令选择一个文件版本。

`git checkout --theirs YOUR_BINARY_FILE`

`git checkout --ours YOUR_BINARY_FILE`

要么选择对方的修改，要么选择自己的修改。







## git status

`git status -s/--short` 将得到一种更为紧凑的格式输出。

例如状态报告输出如下：

``` shell
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

- 新添加的未跟踪文件前面有 ?? 标记
- 新添加到暂存区中的文件前面有 A 标记
- 右边的 M 表示该文件被修改了但是还没放入暂存区
- 左边的 M 表示该文件被修改了并放入了暂存区

例如，上面的状态报告显示： `README`文件在工作区被修改了但是还没有将修改后的文件放入暂存区，`lib/simplegit.rb` 文件被修改了并将修改后的文件放入了暂存区。 而 `Rakefile` 在工作区被修改并提交到暂存区后又在工作区中被修改了，所以在暂存区和工作区都有该文件被修改了的记录。



##  git config

`git config --global -l/--list` List all variables set in config file, along with their values.

`git config --local alias.st 'status'` 创建 `git status` 的别名 `git st` 。

`git config --local --unset alias.st` 会删除这个别名

`git config --local --remove-section user` 会删除 user 下的所有内容，例如 user.name，user.email 等都会被删除。 





> 实际修改的是 config 文件，例如 --local 修改的是 .git/config 文件。



## git add

`git add -u` 更新所有跟踪过的文件，但新创建的文件不会添加到暂存区。而 `git add -A` 会。



## git commit

`git commit -v` 将会出现更详细的对修改了哪些内容的提示，这会将你所做的改变的 diff 输出放到编辑器中从而使你知道本次提交具体做了哪些修改。



## git amend

如果上次提交后没有做任何更改，只是提交说明有问题，想修改提交说明，则直接用 `git commit --amend` 此时会跳出提交说明的编辑框，你直接修改说明并保存退出即可。此时这次提交相对于上次提交只是提交说明不同，这次提交会覆盖上次提交。

如果上次提交后你做了修改，比如修改了文件，或创建了新文件，要将这些修改添加到暂存区，再用 `git commit --amend` ，此时也是跳出提交说明的编辑框，之后同上。此时这次提交多了文件的修改，但依旧会覆盖上次的提交。



--amend

Replace the tip of the current branch by creating a new commit. The recorded tree is prepared as usual (including the effect of the -i and -o options and explicit pathspec), and the message from the original commit is used as the starting point, instead of an empty message, when no other message is specified from the command line via options such as -m, -F, -c, etc. The new commit has the same parents and author as the current one (the --reset-author option can countermand this).

It is a rough equivalent for:

```shell
$ git reset --soft HEAD^
$ ... do something else to come up with the right tree ...
$ git commit -c ORIG_HEAD
```

but can be used to amend a merge commit.

You should understand the implications of rewriting history if you amend a commit that has already been published. (See the "RECOVERING FROM UPSTREAM REBASE" section in [git-rebase](https://devdocs.io/git/git-rebase).)

其中关于 ORIG_HEAD 查看链接 http://blog.csdn.net/dss886/article/details/39271645。

简单的说针对某些危险操作，Git 通过记录HEAD指针的上次所在的位置ORIG_HEAD提供了回退的功能。当你发现某些操作失误了，比如错误的reset到了一个很早很早的版本，可以使用`git reset --hard ORIG_HEAD`回退到上一次reset之前。Git在1.8.5版本之后，加入了HEAD@{}功能，它通过一个链表记录HEAD的移动路径。注意：HEAD@{1}并不一定是ORIG_HEAD！注意到，ORIG_HEAD仅仅是当进行危险操作（比如merge）时才会变更为HEAD指针的原值，而HEAD@{}链表则记录了每次HEAD的移动（包括commit）。





## git rm

`git rm 1\*` 会删除文件 1，12，13等，但不会删除 1.txt，1_test 等文件。`*` 之前的反斜杠 `\`， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。



## git log

最常用：`git log --decorate --oneline --graph --all` 

`-<number>`  `-n <number>`  `--max-count=<number>`  Limit the number of commits to output.

`git log -p` 用来显示每次提交的内容差异，即给出 diff。官方解释：`-p` `-u` `--patch` Generate patch (see section on generating patches).

`git log --stat` 显示每次提交的简略的统计信息。

`git log filename/path 或 --filename/path` 查看文件的历史

`git log --follow filename` Continue listing the history of a file beyond renames (works only for a single file). 即重命名文件后提交，在查看历史时，加 `--follow` 可以查看重命名前文件的历史。

`git log --oneline` 是 `git log --pretty=oneline --abbrev-commit` 的简写。



显示合并的和未合并的提交：

`--merges` Print only merge commits. This is exactly the same as --min-parents=2. 
`--no-merges` Do not print commits with more than one parent. This is exactly the same as --max-parents=1.



`format` 的使用方式：

```shell
$ git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 6 years ago : changed the version number
085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
a11bef0 - Scott Chacon, 6 years ago : first commit
```

**Table 2-1. `git log --pretty=format` 常用的选项**





| 选项    | 说明                         |
| ----- | -------------------------- |
| `%H`  | 提交对象（commit）的完整哈希字串        |
| `%h`  | 提交对象的简短哈希字串                |
| `%T`  | 树对象（tree）的完整哈希字串           |
| `%t`  | 树对象的简短哈希字串                 |
| `%P`  | 父对象（parent）的完整哈希字串         |
| `%p`  | 父对象的简短哈希字串                 |
| `%an` | 作者（author）的名字              |
| `%ae` | 作者的电子邮件地址                  |
| `%ad` | 作者修订日期（可以用 --date= 选项定制格式） |
| `%ar` | 作者修订日期，按多久以前的方式显示          |
| `%cn` | 提交者(committer)的名字          |
| `%ce` | 提交者的电子邮件地址                 |
| `%cd` | 提交日期                       |
| `%cr` | 提交日期，按多久以前的方式显示            |
| `%s`  | 提交说明                       |



**Table 2-2. `git log` 的常用选项**



| 选项                | 说明                                       |
| ----------------- | ---------------------------------------- |
| `-p`              | 按补丁格式显示每个更新之间的差异。                        |
| `--stat`          | 显示每次更新的文件修改统计信息。                         |
| `--shortstat`     | 只显示 --stat 中最后的行数修改添加移除统计。               |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单。                       |
| `--name-status`   | 显示新增、修改、删除的文件清单。                         |
| `--abbrev-commit` | 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。           |
| `--relative-date` | 使用较短的相对时间显示（比如，“2 weeks ago”）。           |
| `--graph`         | 显示 ASCII 图形表示的分支合并历史。                    |
| `--pretty`        | 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。 |



限制输出的选项：

**Table 2-3. 限制 `git log` 输出的选项**

| 选项                       | 说明                |
| ------------------------ | ----------------- |
| `-(n)`                   | 仅显示最近的 n 条提交      |
| `--since`, `--after`     | 仅显示指定时间之后的提交。     |
| `--until`, `--before`    | 仅显示指定时间之前的提交。     |
| `--author`               | 仅显示指定作者相关的提交。     |
| `--committer`            | 仅显示指定提交者相关的提交。    |
| `--grep`                 | 仅显示含指定关键字的提交      |
| `-S`                     | 仅显示添加或移除了某个关键字的提交 |
| `--merges` `--no-merges` | 显示合并或未合并的提交       |

例如：

`git log --author=HonestQiDong` 表示查找作者为 HonestQiDong 的提交

`git log -Skeyword` 表示列出**添加或者删除**了某些关键字的提交的提交

`git log --since="14:06:00" --until="14:20:00"` 表示列出当天下午2:06~2:20之间的历史提交。"14:06:00"可以换位更准确的"2016-12-20:14:06:00" 来表明某一天。



`git log --grep='[0-9]$'` 搜索提交说明是以数字结尾的历史记录。

`--grep=<pattern>` Limit the commits output to ones with log message that matches the specified pattern (regular expression). With more than one `--grep=`, commits whose message matches any of the given patterns are chosen (but see `--all-match`).

`--all-match` Limit the commits output to ones that match all given `--grep`, instead of ones that match at least one.

> 注意：`\d` 可能没用了，需要用 `[[:digit:]]` 代替。或者这样写 `git log --perl-regexp --grep="pattern"` 



**Table 2-4. ECMAScript regular expressions pattern syntax**

| class      | description                             | equivalent (with [regex_traits](http://www.cplusplus.com/regex_traits), default locale) |
| ---------- | --------------------------------------- | ---------------------------------------- |
| [:alnum:]  | alpha-numerical character               | [isalnum](http://www.cplusplus.com/isalnum) |
| [:alpha:]  | alphabetic character                    | [isalpha](http://www.cplusplus.com/isalpha) |
| [:blank:]  | blank character                         | [isblank](http://www.cplusplus.com/isblank) |
| [:cntrl:]  | control character                       | [iscntrl](http://www.cplusplus.com/iscntrl) |
| [:digit:]  | decimal digit character                 | [isdigit](http://www.cplusplus.com/isdigit) |
| [:graph:]  | character with graphical representation | [isgraph](http://www.cplusplus.com/isgraph) |
| [:lower:]  | lowercase letter                        | [islower](http://www.cplusplus.com/islower) |
| [:print:]  | printable character                     | [isprint](http://www.cplusplus.com/isprint) |
| [:punct:]  | punctuation mark character              | [ispunct](http://www.cplusplus.com/ispunct) |
| [:space:]  | whitespace character                    | [isspace](http://www.cplusplus.com/isspace) |
| [:upper:]  | uppercase letter                        | [isupper](http://www.cplusplus.com/isupper) |
| [:xdigit:] | hexadecimal digit character             | [isxdigit](http://www.cplusplus.com/isxdigit) |
| [:d:]      | decimal digit character                 | [isdigit](http://www.cplusplus.com/isdigit) |
| [:w:]      | word character                          | [isalnum](http://www.cplusplus.com/isalnum) |
| [:s:]      | whitespace character                    | [isspace](http://www.cplusplus.com/isspace) |





## git diff

`git diff HEAD~2 -- 1.txt` 指定当前工作区与历史提交中的某个工作区，关于文件 1.txt 的差异。

`git diff HEAD HEAD~2 -- 1.txt` 指定历史提交中某两个提交关于文件 1.txt 的差异。

`git diff HEAD -- 1.txt ` 比较当前工作区与版本库中关于文件 1.txt 的差别。

`--word-diff` 显示一行中的差异。



## git cat-file

git-cat-file - Provide content or type and size information for repository objects

`git cat-file -p 3b18e512d` 通过散列值将对象的内容从对象库中提取出来。

`-p` Pretty-print the contents of object based on its type



## git write-tree

`git write-tree` Create a tree object from the current index

开始时，只有一个 blob 对象，是文件 hello.txt
```shell
$ git ls-files -s
100644 3b18e512dba79e4c8300dd08aeb37f8e728b8dad 0       hello.txt

$ find .git/objects/
.git/objects/
.git/objects/3b
.git/objects/3b/18e512dba79e4c8300dd08aeb37f8e728b8dad
.git/objects/info
.git/objects/pack
```


之后使用 `git write-tree` 从当前 index 创建一个树对象 tree
```shell
$ git write-tree
68aba62e560c0ebc3396e8ae9335232cd93a3f60

$ find .git/objects/
.git/objects/
.git/objects/3b
.git/objects/3b/18e512dba79e4c8300dd08aeb37f8e728b8dad
.git/objects/68
.git/objects/68/aba62e560c0ebc3396e8ae9335232cd93a3f60
.git/objects/info
.git/objects/pack


# 可以看出这个树对象只包含一个文件
$ git cat-file -p 68aba62e560c0ebc3396e8ae9335232cd93a3f60
100644 blob 3b18e512dba79e4c8300dd08aeb37f8e728b8dad    hello.txt
```



## git ls-file

`git ls-files` Show information about files in the index and the working tree


`git ls-files -s/--stage` Show staged contents' object name, mode bits and stage number in the output.

```shell
$ git ls-files -s
100644 3b18e512dba79e4c8300dd08aeb37f8e728b8dad 0       hello.txt
```





## git rev-parse

`git rev-parse master` 查看 SHA-1。





## ssh-keygen

-   `-A`  Generate non-existent host keys for all key types.
-   `-a number`  Number of KDF rounds for new key format or moduli primality tests.
-   `-B`          Show bubblebabble digest of key file.
-   `-b bits`     Number of bits in the key to create.
-   `-C comment`  Provide new comment.
-   `-c`          Change comment in private and public key files.
-   `-D pkcs11`   Download public key from pkcs11 token.
-   `-e`          Export OpenSSH to foreign format key file.
-   `-F hostname` Find hostname in known hosts file.
-   `-f filename` Filename of the key file.
-   `-G file`     Generate candidates for DH-GEX moduli.
-   `-g`          Use generic DNS resource record format.
-   `-H`          Hash names in known_hosts file.
-   `-h`          Generate host certificate instead of a user certificate.
-   `-I key_id`   Key identifier to include in certificate.
-   `-i`          Import foreign format to OpenSSH key file.
-   `-J number`   Screen this number of moduli lines.
-   `-j number`   Start screening moduli at specified line.
-   `-K checkpt`  Write checkpoints to this file.
-   `-k`          Generate a KRL file.
-   `-L`          Print the contents of a certificate.
-   `-l`          Show fingerprint of key file.
-   `-M memory`   Amount of memory (MB) to use for generating DH-GEX moduli.
-   `-m key_fmt`  Conversion format for -e/-i (PEM|PKCS8|RFC4716).
-   `-N phrase`   Provide new passphrase.
-   `-n name,...` User/host principal names to include in certificate
-   `-O option`   Specify a certificate option.
-   `-o`          Enforce new private key format.
-   `-P phrase`   Provide old passphrase.
-   `-p`          Change passphrase of private key file.
-   `-Q`          Test whether key(s) are revoked in KRL.
-   `-q`          Quiet.
-   `-R hostname` Remove host from known_hosts file.
-   `-r hostname` Print DNS resource record.
-   `-S start`    Start point (hex) for generating DH-GEX moduli.
-   `-s ca_key`   Certify keys with CA key.
-   `-T file`     Screen candidates for DH-GEX moduli.
-   `-t type`     Specify type of key to create.
-   `-u`          Update KRL rather than creating a new one.
-   `-V from:to`  Specify certificate validity interval.
-   `-v`          Verbose.
-   `-W gen`      Generator to use for generating DH-GEX moduli.
-   `-y`          Read private key file and print public key.
-   `-Z cipher`   Specify a cipher for new private key format.
-   `-z serial`   Specify a serial number.






## 祖先引用

`HEAD^` 查看当前提交的上一个提交，即“HEAD 的父提交”。

`HEAD^2` 表示当前提交的第二父提交，只适用于合并的提交。第一个提交是你合并时所在的提交。第二个提交是你所合并的提交。

另一种祖先引用是 `~` 。`HEAD~` 与 `HEAD^` 等价。`HEAD~2` 代表“第一个父提交的父提交”，也就是“祖父提交”。当然也可以写成`HEAD^^` 。







## 提交区间

最常用的指明提交区间语法是双点。 这种语法可以让 Git 选出在一个分支中而不在另一个分支中的提交。 

`git log master..experiment` 显示在 experiment 分支中而不在 master 分支中的提交。

`git log origin/master..HEAD` 输出在你当前分支而不在远程 origin 中的提交。

双点语法很好用，但有时候你可能需要两个以上的分支才能确定你所需要的修订，比如查看哪些提交是被包含在某些分支中的一个，但是不在你当前的分支上。 Git 允许你在任意引用前加上 `^` 字符或者 `--not`来指明你不希望提交被包含其中的分支。 因此下列3个命令是等价的：

```shell
$ git log refA..refB
$ git log ^refA refB
$ git log refB --not refA
```

这个语法很好用，因为你可以在查询中指定超过两个的引用，这是双点语法无法实现的。 比如，你想查看所有被 `refA` 或 `refB` 包含的但是不被 `refC` 包含的提交，你可以输入下面中的任意一个命令

```shell
$ git log refA refB ^refC
$ git log refA refB --not refC
```

这就构成了一个十分强大的修订查询系统，你可以通过它来查看你的分支里包含了哪些东西。

最后一种主要的区间选择语法是三点，这个语法可以选择出被两个引用中的一个包含但又不被两者同时包含的提交。 再看看之前双点例子中的提交历史。 如果你想看 `master` 或者 `experiment` 中包含的但不是两者共有的提交，你可以执行

```shell
$ git log master...experiment
```



## 忽略文件

文件 `.gitignore` 的格式规范如下：

- 所有空行或者以 `＃` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式可以以（`/`）开头防止递归。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（`!`）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]` 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`*`) 表示匹配任意中间目录，比如`a/**/z` 可以匹配 `a/z`, `a/b/z` 或 `a/b/c/z`等。

> 注意：* 不会匹配 . 或 _ 等符号

我们再看一个 .gitignore 文件的例子：

```shell
# no .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in the build/ directory
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```

> GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件列表，你可以在 https://github.com/github/gitignore 找到它。



# git 问题



## 二进制文件冲突时

`git checkout --theirs YOUR_BINARY_FILE`

`git checkout --ours YOUR_BINARY_FILE`

要么选择对方的修改，要么选择自己的修改。





## 无法使用 22 端口的 SSH 服务怎么办？

SSH 的默认端口是 22，有时您或您的公司的防火墙会完全屏蔽掉这个端口。如果此时您不方便通过 HTTPS 方式进行 Git 操作，您可以使用 Coding.net 提供的 443 端口的 SSH 服务，您需要确保 SSH 已配置成功，然后执行：

```
$ ssh -T -p 443 git@git-ssh.coding.net
The authenticity of host '[git-ssh.coding.net]:443 ([180.150.178.244]:443)' can't be established.
RSA key fingerprint is SHA256:jok3FH7q5LJ6qvE7iPNehBgXRw51ErE77S0Dn+Vg/Ik.
RSA key fingerprint is MD5:98:ab:2b:30:60:00:82:86:bb:85:db:87:22:c4:4f:b1.
Are you sure you want to continue connecting (yes/no)?
```

输入 yes 即可得到：

```
Hello username You've connected to Coding.net by SSH successfully!
```

此时您就可以通过 `ssh://git@git-ssh.coding.net:443/{username}/{reponame}.git` 的形式进行 Git 操作了。

另外，您还可以修改您的 SSH 配置文件默认使用该方式进行 Git 操作。

只需要修改您的 `~/.ssh/config` 文件即可：

```
Host git.coding.net
Hostname git-ssh.coding.net
Port 443
```

最后您可以通过以下命令测试是否配置正确：

```
$ ssh -T git@git.coding.net
Hello username You've connected to Coding.net by SSH successfully!
```



## 奇葩文件名问题

**.filename.** 这样的文件名，从 `git add` 无法添加。



## 在 linux shell 提示符下显示 git 的分支和状态

**第一步首先clone代码：**

`git clone git://git.kernel.org/pub/scm/git/git.git`

**第二步拷贝脚本：**

把文件  contrib/completion/git-completion.bash  拷到 ~ 下，重命名为  .git-completion.sh

把文件  contrib/completion/git-prompt.sh  拷到 ~ 下，重命名为  .git-prompt.sh

**第三步写入.bashrc以下内容：**

```shell
source ~/.git-completion.sh
source ~/.git-prompt.sh
export GIT_PS1_SHOWDIRTYSTATE=1
export GIT_PS1_SHOWSTASHSTATE=1
export GIT_PS1_SHOWUNTRACKEDFILES=1
export GIT_PS1_SHOWUPSTREAM="verbose git svn"
PS1='\[\033[1;31m\]\u@\h \[\033[1;34m\]\W\[\033[1;31m\]$(__git_ps1 " (%s)")\[\033[1;35m\] -> \[\033[0m\]'
```



