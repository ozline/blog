---
title: OneAPI HPL HPCG的安装与部分Linux笔记
date: 2022-10-07 22:30:00
tags: 大学
---
# OneAPI HPL HPCG的安装与部分Linux笔记

当然要记录下来啦，下回重装的时候还能照着自己写的来装回去呢！

Docker、Vim和Git就不记录啦，这个已经很熟练了，已经在easyhpc上全过一遍啦

# 本机信息

Linux ozline 5.15.0-48-generic #54-Ubuntu SMP Fri Aug 26 13:26:29 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

Ubuntu 22.04.1 LTS

Apple Inc. MacbookPro11,5

Intel Core i7-4980HQ CPU @ 2.80GHz × 8

# OneAPI

## 下载包

`https://www.intel.cn/content/www/cn/zh/developer/tools/oneapi/toolkits.html`

找到`Intel oneAPI Base Toolkit`和`Intel® oneAPI HPC Toolkit`，进入下载页面

操作系统选择Linux，分发选APT软件包管理器

## 安装

- 按上述说明下载后安装，已经是图形界面了，跟着一步一步来就行啦，只需要注意一下
  - 选推荐安装`Recommended Installation`
  - 不使用已有配置`Skip Eclipse IDE configuration`
- 最后会提示`Installization location: `这里表示我们文件存放的位置，后续路径都和这个有关
- 安装好后，编辑`vim ~/.zshrc`
- 添加命令`source /opt/intel/oneapi/setvars.sh intel64`
- 应用`source ~/.zshrc`，可以重启一下终端
- 会跳出一堆信息，这时候就需要我们手动更改`setvars.sh`里的内容了（谁喜欢每次打开终端的时候跳出一堆东西呢）
- 编辑`sudo vim /opt/intel/oneapi/setvars.sh`
  -  首先，在第一行插入`# USING #| EXPLAIN MY MODIFICATION`，对于自行更改的内容要随时做好注释
  -  搜索关键字`initializing oneAPI environment ...`，把这四行全部注释，注意，要用`#|`，便于后续定位更改
  -  搜索关键字`:: $arg_base -- $arg_verz`，注释
  -  搜索关键字`:: oneAPI environment initialized `，把这两行都注释掉
  -  重新打开终端
- 如果一切都没问题，重新打开终端后不会跳出输出的信息，我们可以随便输入一个例如`ifort -v`测试是否成功

## 成果图

无，因为有输出的都被我注释掉了~

# HPL

## 下载包

校园网一直连不上，不知道为何安装了Clash也提示502，后面直接电脑下载传阿里云OSS再在物理机上从OSS下载，曲线救国了

## 安装

### 流程

- 下载`wget http://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz`
- 解压并重命名`tar -xzf hpl-2.3.tar.gz && mv hpl-2.3 hpl`
- 复制`cp setup/Make.Linux_Intel64 Make.test`
- 编辑`Make.test`
  - ==TOPdir==修改为hpl文件夹路径
  - ==ARCH==修改为test
  - ==OMP_DEFS==修改为`-qopenmp`
- 编译`make arch=test`
- 测试`cd /bin/test`，如果前面步骤无误这里会出现一个`HPL.dat`和`xhpl`的二进制文件
- 运行`mpirun ./xhpl`

### 重点分析

在编辑`Make.test`文件上花了较多时间

- 第一次，在`Make.test`文件中只修改了`arch=test`，导致编译过程中遇上链接错误，检查后发现是硬连接地址错误，原因分析后，首先是意识到自己的用户并非root，其次是发现还需要修改`Make.test`中的`TOPdir`，遂更改并重新make
- make后发现还是不行，在这一步选择了把文件夹直接删去，重新解压缩再make
- 第二次，make后没有出现硬链接错误，但是提示`-openmp is not supported`，考虑到自己的物理机系统版本是Ubuntu22.04，同时查证发现这一步需要修改参数`-openmp`为`-qopenmp`，在`Make.test`中将`OMP_DEFS`的参数更改为`-qopenmp`，通过编译

## 成果图

![IMG_9736](https://files.ozline.icu/images/blog/hpc/IMG_9736.jpeg)

# HPCG

## 下载包

过程顺畅，联系到先前HPL一直无法下载，可能是那个域名并不是proxy名单里的，导致没有走代理

## 安装

- 下载`git clone https://github.com/hpcg-benchmark/hpcg.git`
- 进入目录`cd /hpcg/setup/`
- 修改`Make.Linux_MPI`
  - ==TOPdir==考虑到我们是在home目录下clone的仓库，于是我们修改成`$(HOME)/hpcg`
  - ==MPdir==，这里要定位到oneapi的mpi目录，我这里是`/opt/intel/oneapi/mpi/2021.6.0`
  - ==MPinc==，修改为`-I $(MPDir)/include`，利用到了前面我们设定的MPdir
  - ==MPlib==，修改为`$(MPdir)/lib/release/libmpi.a`
- 建目录并进入`mkdir build && cd build`
- 设置环境`/home/ozline/hpcg/configure Linux_MPI`，注意，这里的`configure`就是hpcg文件夹根目录的那个，因此这句命令的configure路径需要你自己确定
- 安装`make`
- 测试`cd bin`，如果成功，会有一个`hpcg.dat`和`xhpcg`文件
- 运行`./xhpcg`，如果没有报出什么error就是跑通了

## 成果图

展示的是文件`HPGC-Benchmark_3.1_{time}.txt`的部分内容

![IMG_9738](https://files.ozline.icu/images/blog/hpc/IMG_9738.jpeg)

# Linux

## Linux基础

### 快捷键

- Tab : 命令补全
- Ctrl+D ： 键盘输入结束或退出终端。
- Ctrl+S ： 暂停当前程序，暂停后按下任意键恢复运行。
- Ctrl+Z ： 将当前程序放到后台运行，恢复到前台为命令fg。
- Ctrl+A ： 将光标移至输入行头，相当于Home键。
- Ctrl+E ： 将光标移至输入行末，相当于End键。
- Ctrl+K ： 删除从光标所在位置到行末。
- Alt+Backspace ： 向前删除一个单词。

- Shift+PgUp ： 将终端显示向上滚动。
- Shift+PgDn ： 将终端显示向下滚动。

### APT

- apt-cache search <_software_> ： 查询软件库是否有这个软件
- apt-get -reinstall install <_software_> ： 在软件被破坏时重新安装这个软件
- apt-get purge <_software_> ： 卸载软件，连同配置文件

### 用户

- whoami ： 输出当前终端的用户名
- adduser <_username_> ： 创建用户
- su <_username_> ： 切换到user
- su - <_username_> ： 切换到user，同时环境变量也切换到目标用户的
- groups <_username_> ： 输出用户和用户组
  - 样例输出： `ozline : ozline adm cdrom sudo dip plugedv lpadmin lxd sambashare`
- deluser <user_name>  --remove-home ：删除用户

### 文件

- chown <_newowner_> <_filename_> ：变更文件所有者
- chmod <_permissions_> <_filename_> ：变更文件权限
  - 第一种方式：二进制数字表示。每个文件的三组权限对应一个 “rwx”，每一位可以用 0 或 1 表示是否有权限，例如只有读和写权限的话就是 “rw-” - “110”，所以"rwx"对应的二进制"111"就是十进制的7。
  - `chmod 700 <filename>`·
  - 第二种方式：加减赋值表示。假设一开始 file 文件的权限是 “rwxr–r--” 完成上述同样的效果，还可以：
  - `chmod go-rr <filename>`
- find [path] [option] [action]
  - find /usr/ -name myfile ：在 /usr/ 下搜索 名为myfile的文件或目录
- locate：
  - locate /usr/a ： 查找 /user和它的子目录下所有 a 开头的文件

### 解压缩

#### RAR

- rar a <_filename_> <_dirname_or_filename_> ：把目录/文件添加进压缩包
- rar d <_filename_> <_filename_> ： 从压缩包内删除文件
- rar l <_filename_> ： 查看压缩包内容
- unrar x <_filename_> ： 保持压缩文件的目录结构解压缩
- unrar e <_filename_> ： 将所有文件解压到当前目录

#### ZIP

- zip -r -o <_filename_>  <_dirname_> ： 把目录打包成压缩包，其中`-r`表示递归，`-o`表示输出到，还可额外添加`-e`进行加密，如果期望Linux压缩文件在Windows下正常解压，可以加上`-l`参数
- unzip <_filename_> ：解压，若加上`-l`则不解压只查看

#### TAR

- tar -cf <_filename_> <_dirname_> ： 打包，其中`-c`表示创建，`-f`表示指定文件名
- tar -tf <_filename_> ： 查看包内文件
- tar -xf <_filename_> -C <_dirname_> ： 解包到指定目录
- tar -czf <_filename_> <_dirname_> ：打包并压缩，这里用到了gzip
- tar -xzf <_targz_name_> ： 解压缩.tar.gz文件

### 管道与文本处理

- && 表示如果前一个命令返回的状态为0（这些状态码有一套默认的规定），则执行后一个命令，否则不执行。而 || 则与之相反。
- 在命令行中使用匿名管道，通常用`|`来表示，以`ls -a | grep mysql`为例，先执行了`ls -a`，然后把输出结果作为输入执行`grep mysql`命令，在输入中查找包含`mysql`关键字的文件
- grep
  - grep -r <_keywords_> <_dirname_> ：搜索目录下所有包含关键字的文件
- wc ： 统计并输出一个文件中行、字节等数据的数目，其中`-l`表示行数，`-c`表示字节数
  - ls -a | wc -l ：统计文件个数
- uniq： 去重
  - cat words | uniq ：除去一些重复行，但是uniq只能去除连续重复的行，所以我们一般要先用一个sort，也就是`cat words | sort | uniq`
- sort： 排序
  - ls -a | sort：字典排序
  - ls -a | sort -r ：反转排序
- col：tab转空格
  - 例：cat <_filename_> | col -x | cat -A ：把文件的tab转成空格
- tr：替换
  - echo "Hello World" | tr -d "el"：删去文本中的所有e和l（不是el）
  - echo "Hello World" | tr '[a-z]' '[A-Z]：支持正则表达式，把小写转大写'

### 重定向

- 在Linux中，标准输入、标准输出、标准错误的文件描述符为0、1、2
- cat words | sort | uniq 1>/dev/null 2>&1：输出到/dev/null的表示丢弃输出

### 前后台切换

- 可以利用`&`这个符号，让命令在后台中运行，例如`ls &`
- jobs：查看后台的工作，可以看到类似于`[1] + suspended ...(command)`，分别为编号，状态和命令本身，这里面的`+`号表示的是最后一个被转入后台的工作，而`-`号表示倒数第二个， 其余的不会显示
- fg %<jobid>：把后台工作拿回前台，例如`fg %1`

## GCC

### 编译参数

这里还参考了这篇文章:https://www.jianshu.com/p/fc8ca10971ed

补充了一些觉得经常用的

- `-c`：只编译，不链接，生成`.o`文件，通常用于编译不包含主程序的子程序文件（只激活预处理、编译、汇编）
- `-o <filename>`：指定输出文件名，否则gcc将会给出预设可执行文件`a.out`
- `-g`：生成测试信息，调试需要加入这个选项
- `-O`：对程序优化编译链接，O0、O、O2、O3优化递增
- `-Idirname`：将dirname所指出的目录加入到程序头文件目录列表中，是在预编译过程中使用的参数
- `-x <language> <filename>`：指定编译语言编译
- `-S`：只激活预处理和编译，变为汇编代码
- `-E`：只激活预处理
- `-shaer`：尽量使用动态库，使得生成文件较小
- `-static`：不使用动态库，生成可以直接运行不需要任何依赖的静态库
- `-Wall`：生成所有警告信息
- `-w`：不生成任何警告
- `-m486`：针对486进行代码优化
- `-nostdinc`：不在系统默认的头文件目录里找头文件，一般和`-I`一起用，指定头文件位置

## GDB

- 输入`gdb`就可以与deb进行交互，使用`gdb -tui`则可以将屏幕分成两个部分，上方显示源代码，后续内容全部为在gdb环境内的操作
- `help`：输出帮助文档
- `file <filename>`：载入可执行程序
- `set args`：命令修改发送给程序的参数
- `list <line> `：列出行号附近的代码
- `next <N>`：执行N次下一步，可以省略N，表示执行1次，这不会进入函数内部
- `step <N>`：执行N次下一步，可以进入函数内部
- `call <funcname>`：调用函数
- `break <line>`：设置某一行断点
- `info <things>`：获取信息，things指代下述命令
  - `breakpoints`：查询断点信息，会列出一个表格，从左到右分别表示`序号`，`类型`，`断点命中后，该断点是保留、删除还是关闭`、`可用状态`、`虚拟内存地址`、`源文件信息(包括行数)`
  - `local`：获取变量信息（不包括全局变量）
- `delete <number>`：删除指定序号的断点
- `enable/disable <number>`：启用/关闭断点
- `run`：开始调试
- `print <args>` ：输出指定变量名当前信息
- `finish`：执行程序到当前函数结束
- `continue`：到下一个断点
- `until <line>`：执行到代码的某一行

## Shell基础

- 第一行通常有一个`#!/bin/bash`，头两个字符代表一个标记，表示后面的脚本所使用的解释器
- 设定一个变量，注意是变量名与等号之间不能有空格，命名只能使用英文字母、数字、下划线，不能使用特殊关键字
- 我们利用`>`输出重定向，例如我们使用`date > date.txt`来输出到txt文件
- 所以，我们可以用`<`表示输入重定向

### 运算符

```shell
#!/bin/bash
a=5
b=10

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"
```

- expr是一个表达式计算工具，注意使用的是==反引号==

### 输出

- 基本和c++的printf一样
- 输出命令：使用反引号，实际上涉及到与命令相关的，基本上都是反引号（？）
- 输出数组
  - 首先，我们需要定义一个数组`array_list = (E H P C)`
  - 在echo中，我们使用`${array_list[0]}`来输出，类似php（？）

### if-else

```shell
#!/bin/sh
a=5
b=6
if [ $a == $b ]
then
   echo "a == b"
else
   echo "a !=b"
fi
```

### while

```shell
#!/bin/sh
count=1
while(( $count<=3 ))
do
    echo $count
    let "count++"
done
```

### for

```shell
#!/bin/sh
for str in 'ehpc is easy hpc'
do
    echo $str
done
```

### until

```shell
#!/bin/bash
val=0
until [ ! $val -lt 5 ]
do
   echo $val
   val=`expr $val + 1`
done
```

### function

```shell
#! /bin/bash
function echo_hello()
{
  echo "hello world"
}
echo_hello
```

其实就是一种格式啦，这边直接贴代码方便忘记的时候回来查，就像Java、Go和JavaScript的for都长得不尽相同一样

