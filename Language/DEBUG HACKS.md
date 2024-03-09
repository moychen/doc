# DEBUG HACKS

## 第1章 热身准备

**程序异常结束时的应对方法**

<img src="images/DEBUG HACKS/image-20230806173918141.png" alt="image-20230806173918141" style="zoom:67%;" />

**程序不结束时的应对方法**

<img src="images/DEBUG HACKS/image-20230806173756755.png" alt="image-20230806173756755" style="zoom: 67%;" />

**表 1-1 内核有问题的现象**

| 区分方法     | 结果                               |
| ------------ | ---------------------------------- |
| ps           | 显示中途停止                       |
|              | 状态为D                            |
| ping         | 不返回响应                         |
| 键盘         | 键盘无法输入                       |
| kill -9      | 无法结束程序                       |
| strace       | 无法附加（attach）到进程（无响应） |
| gdb          | 无法附加（attach）到进程（无响应） |
| 查看内核信息 | softlockup 等有输出结果            |

## 第2章 调试前的必知必会

### 2.1  获取进程的内核存储

```shell
# 检查当前的内核转储功能是否有效
$ ulimit -c 

# 设置内核转储文件的大小限制
# 不限制内核转储文件的大小
$ ulimit -c unlimited 

# 设置内核转出文件的保存位置
# 设置 /etc/sysctl.conf 中 kernel.core_pattern 变量，sysctl -p
$ cat /etc/sysctl.conf | grep core_pattern 
kernel.core_pattern = /var/core/%t-%e-%p-%c.core

# 转储文件名后面是否会增加PID后缀
$ cat /etc/sysctl.conf | grep core_uses_pid
kernel.core_uses_pid = 0 

$ sysctl -p
```

**表 2-1 kernel.core_pattern 中可以设置的格式符**

| 格式符 | 说明                                           |
| ------ | ---------------------------------------------- |
| %%     | %字符本身                                      |
| %p     | 被转储进程的进程ID（PID）                      |
| %u     | 被转储进程的真实用户ID（real UID）             |
| %g     | 被转储进程的真实组ID（real GID）               |
| %s     | 引发转储的信号编号                             |
| %t     | 转储时刻（timestamp）                          |
| %h     | 主机名（同 uname(2) 返回的nodename）           |
| %e     | 可执行文件名                                   |
| %c     | 转储文件的大小上限（内核版本2.6.24以后可以用） |

```shell
# 使用用户模式辅助程序自动压缩内核转储文件
$ cat /etc/sysctl.conf
kernel.core_pattern = |usr/local/sbin/core_helper %t %e %p %c

$sysctl-p
```

**core_helper:**

```shell
#!/bin/sh
exec gzip ->/var/core/$1-$2-$3-$4.core.gz
```

```shell
# 启动整个系统的内核转储功能
# 或直接修改/etc/security/limits.conf
$ cat /etc/sysconfig/init | grep DAEMON_COREFILE_LIMIT
DAEMON_COREFILE_LIMIT="unlimited"

# 使被 SUID 的程序也能内核转储
$ cat /etc/sysctl.conf | grep suid_dumpable 
fs.suid_dumpable = 1
```

```shell
# 利用内核转储掩码排除共享内存
# 多个进程使用同一个共享内存，共享内存达到几个G，当所有进程的共享内存全部转储的话，会生成很大的文件，对磁盘和系统都有很大的影响，转储过程中会导致系统停止服务时间过长。
# 由于各个共享内存的进程中，共享内存的内容是相同的，所以只设置成只在某个进程中转储共享内存即可
# 设置方法： /proc/<PID>/coredump_filter, coredump_filter 使用比特掩码表示内存类型
$ cat /proc/16278/coredump_filter
0000003

# 要跳过所有的共享内存区段，可将此值设置为1
$ echo 1 > coredump_filter
```

**表 2-2 比特掩码对应的内存类型**

| 比特掩码 | 内存类型                                          |
| -------- | ------------------------------------------------- |
| 比特 0   | 匿名专用内存                                      |
| 比特 1   | 匿名共享内存                                      |
| 比特 2   | file-backed 专用内存                              |
| 比特 3   | file-backed 共享内存                              |
| 比特 4   | ELF 文件映射 （内核版本2.6.24以后的版本可以使用） |

### 2.2 GDB 的基本使用方法

**参考文档：**[GDB: The GNU Project Debugger](https://sourceware.org/gdb/)

```
gdb [-help] [-nh] [-nx] [-q] [-batch] [-cd=dir] [-f] [-b bps]
    [-tty=dev] [-s symfile] [-e prog] [-se prog] [-c core] [-p procID]
    [-x cmds] [-d dir] [prog|prog procID|prog core]
```

#### run gdb

#### break (b) - 设置断点

```
b 函数名
b 行号
b 文件名:行号
b 文件名:函数名
b +偏移量
b -偏移量
b *地址
```

```
(gdb) info b
```

**其他断点**

* hbreak - 硬件断点
* tbreak - 临时断点
* thbreak - 临时硬件断点
* b 断点 if 条件 - 条件断点

#### disable (dis)/enable - 删除断点和禁用断点

```
disable：临时禁用所有断点
disable 断点编号
disable display 显示编号：禁用display命令定义的自动显示
disable mem 内存区域：禁用mem命令定义的内存区域

enable：重新启用禁用的所有断点
enable 断点编号
enable once 断点编号：使指定的断点只启用一次
enable delete 断点编号
enable display 显示编号
enable mem 内存区域
```

#### clear/delete (d) - 删除断点和监视点

```
clear
clear 函数名
clear 行号
clear 文件名:行号
clear 文件名:函数名
delete <编号>

编号是指断点或监视点的编号
```

<img src="images/DEBUG HACKS/image-20230808000757457.png" alt="image-20230808000757457" style="zoom: 80%;" />

#### commands - 断点命令

`commands` 命令可以定义在断点中断后自动执行的命令，`silent ` 使 GDB 更安静的触发断点，简化输出，`silent` 必须是命令列表的第一个命令，如果命令列表中最后一个命令是 `continue`，GDB 将在命令列表执行完成后继续自动执行程序。

```
commands 断点编号
	命令
	...
end
```

```
commands 断点编号
	silent
	printf "x is %d\n", x
	cont
end
```

#### run (r) - 运行程序

```
run 参数
```

```
(gdb) run -a
```

**别名：**

* start

#### info (i) - 显示信息

#### condition - 给指点断点添加或删除触发条件

```
condition 断点编号：删除断点的触发条件
condition 断点编号 条件：给断点添加触发条件
```

#### backtrace (bt) - 显示栈帧

```
bt：显示所有栈帧
bt N：显示开头N个栈帧  
bt -N：显示最后N个栈帧
bt full：不仅显示栈帧，还有局部变量，N与上述意思相同
bt full N
bt full -N
```

**别名：**

* where
* info stack

#### print (p) - 显示变量

```
p 变量
p /格式 变量
```

**表2-3 显示寄存器可使用的格式**

| 格式 | 显示寄存器可使用的格式                              |
| ---- | --------------------------------------------------- |
| x    | 十六进制数                                          |
| d    | 十进制数                                            |
| u    | 无符号十进制数                                      |
| o    | 八进制数                                            |
| t    | 二进制数（two）                                     |
| a    | 地址                                                |
| c    | 字符（ASCII）                                       |
| f    | 浮点小数                                            |
| s    | 字符串                                              |
| i    | 机器语言（仅在显示内存的 `x` 命令中可用）汇编语言？ |

#### x - 显示内存

`x` 这个命令的由来是 eXamining，一般格式为：

```
x/NFU 地址

N - 重复次数
F - 表 2-3 中列示的格式
U - 表 2-4 中所示的单位
```

**表 2-4 U代表的单位**

| 单位 | 说明                  |
| ---- | --------------------- |
| b    | 字节                  |
| h    | 半字（2字节）         |
| w    | 字（4字节）（默认值） |
| g    | 双字（8字节）         |

#### info registers (info reg) - 显示寄存器

```
(gdb) info reg
rax            0x0      0
rbx            0x0      0
rcx            0x60     96
rdx            0x7ffc62e09818   140721967372312
rsi            0x7ffc62e09808   140721967372296
rdi            0x1      1
rbp            0x7ffc62e09720   0x7ffc62e09720
rsp            0x7ffc62e09720   0x7ffc62e09720
r8             0x7fd1d442ce80   140539186040448
r9             0x0      0
r10            0x7ffc62e090e0   140721967370464
r11            0x7fd1d409df30   140539182309168
r12            0x400560 4195680
r13            0x7ffc62e09800   140721967372288
r14            0x0      0
r15            0x0      0
rip            0x400664 0x400664 <main(int, char const**)+23>
eflags         0x10246  [ PF ZF IF RF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
gs             0x0      0
```

在寄存器名之前添加 $，即可显示各寄存器的内容。

```
(gdb) p $rdx
$2 = 140721967372312
```

#### disassemble (disas) - 反汇编

```
disassemble：反汇编当前整个函数
disassemble 程序计数器：反汇编程序计数器所在函数的整个函数
disassemble 开始地址 结束地址：反汇编从开始地址到结束地址之前的部分
```

```
(gdb) disassemble 
Dump of assembler code for function main(int, char const**):
   0x000000000040064d <+0>:     push   %rbp
   0x000000000040064e <+1>:     mov    %rsp,%rbp
   0x0000000000400651 <+4>:     mov    %edi,-0x14(%rbp)
   0x0000000000400654 <+7>:     mov    %rsi,-0x20(%rbp)
   0x0000000000400658 <+11>:    movq   $0x0,-0x8(%rbp)
   0x0000000000400660 <+19>:    mov    -0x8(%rbp),%rax
=> 0x0000000000400664 <+23>:    movl   $0x1,(%rax)
   0x000000000040066a <+29>:    mov    $0x0,%eax
   0x000000000040066f <+34>:    pop    %rbp
   0x0000000000400670 <+35>:    retq   
End of assembler dump.
```

#### next (n)/step (s) - 单步执行

```
n
s
n 次数：执行多次
s 次数：执行多次
```

**其他：**

* nexti (ni)/stepi (si) - 逐条执行汇编指令
* ni 次数/si 次数：执行多次

#### continue (c/cont) - 继续运行

```
c
c 次数
```

#### watch (w) - 监视点

```
watch 表达式：表达式发生变化时暂停运行
awatch 表达式：表达式被访问、改变时暂停运行
rwatch 表达式：表达式被访问时暂停运行

表达式指常量或变量等
```

#### set - 设置变量

```
set variable <变量>=<表达式>
set $变量名：定义变量，变量以$开头，由英文字母和数字组成
```

#### generate-core-file (gcore) - 生成内核转储文件

```
(gdb) generate-core-file
```

#### gcore - 生成内核转储文件

```shell
gcore [-a] [-o filename] pid
```

#### attach - attach 到进程

```
attach pid
```

#### info proc - 显示进程信息

```
(gdb) info proc
```

#### finish/until (u) - 暂停调试

```
finish：执行完当前函数后暂停
unitl：执行完当前函数等代码块后暂停，如果是循环，则在执行完循环后暂停
until 地址
```

#### list (l) - 显示函数或代码行

#### print-object (po) - 显示目标信息

#### sharedlibrary (share) - 加载共享库的符号

#### frame (f) - 选择要显示的栈帧

#### up/down (do) - 在当前调用的栈帧中选择要显示的栈帧

#### forward - search (fo) - 向前搜索

#### edit (e) - 编辑文件或函数

#### directory (dir) - 插入目录

#### 值的历史

#### 命令历史

#### 初始化文件（.gdbinit）

#### 命令定义 (define)



### 2.3 Intel 架构的基本知识

####  2.3.1 字节序（Little Endian & Big Endian）

**Little Endian : **低位数据排在内存低地址。

**Big Endian : **高位数据存放再低地址。

 #### 2.3.2 32 位环境寄存器

#### 2.3.3 64 位环境寄存器

#### 2.3.4 数据类型



