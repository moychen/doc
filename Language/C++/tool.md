# Build Tool

## 1. GNU (gcc & g++)

### 1.1 g++简介

g++是GNU开发的C++编译器，是GCC（GNU Compiler Collection）GNU编译器套件的组成部分。另外，gcc是GNU的C编译器。

看官方手册你会发现g++的命令选项真的多如繁星，令人头皮发麻。但是常用的命令选项也就那几个，完成我们的日常编译，g++使用起来还是比较简单的！g++编译器是GCC的一部分，GCC编译工作一般分为四个步骤：  

（1）预处理（Preprocessing）。由预处理器cpp完成，将.cpp源文件预处理为.i文件。

```javascript
g++  -E  test.cpp  -o  test.i    //生成预处理后的.i文件
```

（2）编译（Compilation）。将.i文件编译为.s的汇编文件。使用`-S`选项，只进行编译而不进行汇编，生成汇编代码。这里的编译器具体是什么，我暂时还不清楚，知道的请留言告知，万分感谢。百度百科说是egcs，但是我在Linux并没有查到该命令。

```javascript
g++ -S test.i -o test.s         //生成汇编.s文件
```

（3）汇编（Assembly）。由汇编器as完成，将.s文件汇编成.o的二进制目标文件。

```javascript
g++  -c  test.s  -o  test.o    //生成二进制.o文件
```

（4）链接（Linking）。由链接器ld，将.o文件连接生成可执行程序。

```javascript
g++ test.o  -o  test.out      //生成二进制.out可执行文件 
```

### 1.2 命令格式

```javascript
gcc [-c|-S|-E] [-std=standard]
    [-g] [-pg] [-Olevel]
    [-Wwarn...] [-pedantic]
    [-Idir...] [-Ldir...]
    [-Dmacro[=defn]...] [-Umacro]
    [-foption...] [-mmachine-option...]
    [-o outfile] [@file] infile...
```

### 1.3 命令选项

关于g++的命令选项，大家可以参考[g++百度百科](http://baike.baidu.com/link?url=FQvGegKMC9UsRPbdBRSRkto7y-QJuy093kei3dqlVwzghhwZv_i3nD53Xtq16n4_26phqLxD4DKCqnXSQ17Az_)或者[GCC官方手册](https://gcc.gnu.org/onlinedocs/gcc-6.1.0/gcc.pdf)，或者使用`man g++`单独查看g++使用手册。

下面列出常用的命令选项。

**（1）总体选项**

```javascript
-E
    只激活预处理,这个不生成文件,你需要把它重定向到一个输出文件里面。例子用法:   
    gcc -E hello.c > pianoapan.txt   
    gcc -E hello.c | more   
    慢慢看吧,一句`hello word`也要预处理成800行的代码。     
-S   
    只激活预处理和编译，就是指把文件编译成为汇编代码。例子用法： 
    gcc -S hello.c   
    将生成.s的汇编代码，可以用文本编辑器查看。    
-c    
    只激活预处理,编译,和汇编,也就是他只把程序做成obj文件。例子用法:   
    gcc -c hello.c   
    将生成.o的目标文件（object file）。 
-o
    指定目标名称，缺省的时候，gcc/g++编译出来的文件是a.out。例子如下：   
    g++ -o hello.out hello.cpp
    g++ -o hello.asm -S hello.cpp   

g++ -std=c++0x test.cpp -Wl,-R /home/ufccode/appcom -L /home/ufccode/appcom -ls_libpublic_uft
```

**（2）目录选项**

```javascript
-I[dir]
    在你是用#include "file"的时候，gcc/g++会先在当前目录查找你所指定的头文件，如果没有找到，会到系统默认的头文件目录找。如果使用-I指定了目录，编译器会先在指定的目录查找，然后再去系统默认头文件目录查找。对于#include <file>，gcc/g++会到-I指定的目录查找，查找不到，然后再到系统默认的头文件目录查找。
-include [file]
    相当于“#include”，用于包含某个代码,简单来说,就是编译某个文件,需要另一个文件的时候,就可以   
    用它设定,功能就相当于在代码中使用#include。例子用法:   
    gcc hello.c -include /root/pianopan.h   
-I-
    就是取消前一个参数的功能,所以一般在-Idir之后使用   
-idirafter [dir]   
    在-I的目录里面查找失败，将到目录dir里面查找。
-iprefix [prefix]，-iwithprefix [dir]
    一般一起使用，当-I的目录查找失败，会到prefix+dir下查找。
-L[dir]   
    编译的时候，指定搜索库的路径。比如你自己的库，可以用它指定目录，不然编译器将只在标准库的
    目录找。这个dir就是目录的名称。
-l[library]    
    指定编译的时使用的库，例子用法   
    gcc -lcurses hello.c   
    使用curses库编译连接，生成程序。  
```

**（3）预处理选项**

```javascript
-Dmacro
    相当于C语言中的#define macro。
-Dmacro=defn   
    相当于C语言中的#define macro=defn。
-Umacro
    相当于C语言中的#undef macro。
-undef
    取消对任何非标准宏的定义。
```

**（4）链接方式选项**

```javascript
-static
    此选项将禁止使用动态库。优点：程序运行不依赖于其他库。缺点：可执行文件比较大。
-shared
    此选项将尽量使用动态库，为默认选项。优点：生成文件比较小。缺点：运行时需要系统提供动态库。
-symbolic
    建立共享目标文件的时候，把引用绑定到全局符号上。对所有无法解析的引用作出警告（除非用连接选项，
    '-Xlinker -z -Xlinker defs'取代)。注：只有部分系统支持该选项。
-Wl,-Bstatic
    告诉链接器ld只链接静态库，如果只存在动态链接库，则链接器报错。
-Wl,-Bdynamic
    告诉链接器ld优先使用动态链接库，如果只存在静态链接库，则使用静态链接库。
```

**（5）错误与告警选项**

```
-Wall
    一般使用该选项，允许发出GCC能够提供的所有有用的警告。也可以用-W{warning}来标记指定的警告。
-pedantic
    允许发出ANSI/ISO C标准所列出的所有警告。
-pedantic-errors
    允许发出ANSI/ISO C标准所列出的错误
-werror
    把所有警告转换为错误，在警告发生时中止编译过程。
-w
    关闭所有警告,建议不要使用此项。
-WFATAL-ERRORS
	在出现错误的时候停止编译
--fmax-errors=N
	在第N次出现错误的时候停止编译
```

**（6）调试选项**

```javascript
 -g   
    指示编译器，在编译时，产生调试信息。
-gstabs   
    此选项以stabs格式生成调试信息,但不包括gdb调试信息。 
-gstabs+   
    此选项以stabs格式声称调试信息,并且包含仅供gdb使用的额外调试信息.   
-ggdb    
    此选项将尽可能的生成gdb可以使用的调试信息。
-glevel
    请求生成调试信息，同时用level指出需要多少信息，默认的level值是2。
```

**（7）优化选项**

```javascript
-O0   
-O1   
-O2   
-O3   
    编译器优化选项分为4个级别，-O0表示没有优化，-O1为缺省值，建议使用-O2，-O3优化级别最高。
```

**（8）其他选项**

```javascript
-fpic
    编译器就生成位置无关目标码.适用于共享库(shared library).
-fPIC
    编译器就输出位置无关目标码.适用于动态连接(dynamic linking),即使分支需要大范围转移。
-v 显示详细的编译、汇编、连接命令
-pipe
    使用管道代替编译过程中的临时文件,在使用非gnu汇编工具的时候,可能有些问题   
    g++ -pipe -o hello.out hello.cpp
-ansi
    关闭gnu c中与ansi c不兼容的特性，激活ansi c的专有特性(包括禁止一些asm inline typeof关键字,以及
    UNIX,vax等预处理宏。
-fno-asm   
    此选项实现ansi选项功能的一部分，它禁止将asm,inline和typeof用作关键字。   
-fno-strict-prototype
    只对g++起作用,使用这个选项,g++将对不带参数的函数,都认为是没有显式的对参数的个数和类型说明,而不是没有
    参数.而gcc无论是否使用这个参数,都将对没有带参数的函数,认为没有显式说明的类型。
-fthis-is-varialble   
    就是向传统c++看齐,可以使用this当一般变量使用。
-fcond-mismatch   
    允许条件表达式的第二和第三参数类型不匹配,表达式的值将为void类型。
-funsigned-char   
-fno-signed-char   
-fsigned-char   
-fno-unsigned-char   
    这四个参数是对char类型进行设置,决定将char类型设置成unsigned char(前两个参数)或者signed char(后
    两个参数)。
-imacros file   
    将file文件的宏,扩展到gcc/g++的输入文件,宏定义本身并不出现在输入文件中     
-nostdinc   
    使编译器不在系统缺省的头文件目录里面找头文件,一般和-I联合使用,明确限定头文件的位置。 
-nostdin C++
    规定不在g++指定的标准路经中搜索,但仍在其他路径中搜索,此选项在创建libg++库使用。
-C
    在预处理的时候,不删除注释信息,一般和-E使用,有时候分析程序，用这个很方便的。 
-M  
    生成文件依赖的信息，包含目标文件所依赖的所有源文件。你可以用gcc -M hello.c来测试一下，很简单。   
-MM   
    和上面的那个一样，但是它将忽略由#include造成的依赖关系。   
-MD
    和-M相同，但是输出将导入到.d的文件里面。
-MMD   
    和-MM相同，但是输出将导入到.d的文件里面。
-Wa,option   
    此选项传递option给汇编程序；如果option中间有逗号,就将option分成多个选项，然后传递给会汇编程序。 
-Wl.option   
    此选项传递option给连接程序;如果option中间有逗号,就将option分成多个选项,然后传递给会连接程序。
-x language filename   
    设定文件所使用的语言,使后缀名无效,对以后的多个有效.也就是根据约定C语言的后缀名称是.c的，而C++的后缀
    名是.C或者.cpp。如果你很个性，决定你的C代码文件的后缀名是.pig，那你就要用这个参数,这个参数对他后面
    的文件名都起作用，除非到了下一个参数的使用。可以使用的参数有下面的这些：
    c,objective-c,c-header,c++,cpp-output,assembler,assembler-with-cpp。   
    看到英文，应该可以理解的。例子用法:   
    gcc -x c hello.pig
-x none filename
    关掉上一个选项，也就是让gcc根据文件名后缀，自动识别文件类型，例子用法:   
    gcc -x c hello.pig -x none hello2.c
```

### 1.4 FAQ

#### 4.1编译选项疑问

##### 4.1.1-Wno-unknown-pragmas和-Wno-format -pg

**-Wno-unknown-pragmas：**查了大量资料和官方的手册，我觉得这个应该是实验室的师兄写错了，貌似没有这个警告命令选项。官方手册中有如下两个设置警告的命令选项。

**（1）-Wunknown-pragmas**

```javascript
Warn when a #pragma directive is encountered that is not understood by GCC. If this 
command-line option is used, warnings are even issued for unknown pragmas in system 
header files. This is not the case if the warnings are only enabled by the ‘-Wall’ 
command-line option.
```

遇到GCC无法识别的编译指导指令，发出警告。在使用了-Wall选项时，就不需要使用该命令选项了。

**（2）-Wno-pragmas**

```javascript
Do not warn about misuses of pragmas, such as incorrect parameters, invalid
syntax, or conflicts between pragmas. See also '-Wunknown-pragmas'.
```

遇到GCC无法识别的编译指导指令，不发出警告。

**-pg作用：**编译的过程中加入额外的代码， 供性能分析工具gprof剖析程序的耗时情况。

#### 4.2链接注意事项

##### 4.2.1指定静态与动态的链接方式

g++链接库时，默认优先链接动态链接库。静态库与动态库混合链接时，有如下两种方法：  （1）静态链接库使用绝对路径，动态链接库使用-l。以boost库为例，如果我们要使用静态库则可书写如下：

```javascript
 g++ main.cpp -pthread /usr/lib64/libboost_thread.a /usr/lib64/libboost_system.a
```

（2）使用`-Wl,-Bstatic`告诉链接器`ld`链接静态库，不存在静态库，则`ld`报错，只存在动态链接库也报错。使用`-Wl,-Bdynamic`告诉链接器**优先**使用动态链接库，如果只存在静态库，则链接静态库，不报错。示例如下：

```javascript
g++  main.cpp -Wl,-Bstatic -lboost_system -lboost_thread -Wl,-Bdynamic
```

**注意：**  （1）命令末尾`-Wl,-Bdynamic`，作用是告诉链接器，后续系统库的链接默认使用动态链接，否则会出现找不到系统库的错误，诸如：

```javascript
/usr/bin/ld: cannot find -lgcc_s
collect2: ld returned 1 exit status
```

（2）链接时，库要放在目标文件的后面，否则会报”undefined reference to: xxx”错误。具体参见gcc手册的如下描述：

```javascript
the linker searches and processes libraries and object files in the order they are 
specified. Thus, `foo.o -lz bar.o' searches library `z' after file foo.o but before 
bar.o. If bar.o refers to functions in `z', those functions may not be loaded.
```

### 参考文献

[gcc及其选项详解](http://blog.chinaunix.net/uid-25119314-id-224398.html)  

[GCC官方手册](https://gcc.gnu.org/onlinedocs/gcc-6.1.0/gcc.pdf)  

[gcc编译选项](http://www.cnblogs.com/fengbeihong/p/3641384.html)  

[gcc/g++ 静态动态库混链接](http://blog.csdn.net/wangxvfeng101/article/details/15336955) 

[折腾gcc/g++链接时.o文件及库的顺序问题](http://blog.csdn.net/imilli/article/details/51454236)  

[g++参数介绍](http://www.cnblogs.com/lidan/archive/2011/05/25/2239517.html)  

[gcc cannot find cc1plus](https://stackoverflow.com/questions/36353302/gcc-cannot-find-cc1plus?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

### 1.5. 手工安装

```bash
# GMP: https://gcc.gnu.org/pub/gcc/infrastructure/
$ ./configure --prefix=/usr/local/gmp-6.1.0/

# MPFR: https://gcc.gnu.org/pub/gcc/infrastructure/
$ ./configure --prefix=/usr/local/mpfr-4.1.0/     --with-gmp-include=/usr/local/gmp-6.1.0/include     --with-gmp-lib=/usr/local/gmp-6.1.0/lib

# MPC: https://gcc.gnu.org/pub/gcc/infrastructure/
$ ./configure --prefix=/usr/local/mpc-1.2.1     --with-gmp-include=/usr/local/gmp-6.1.0/include --with-gmp-lib=/usr/local/gmp-6.1.0/lib --with-mpfr-include=/usr/local/mpfr-4.1.0/include --with-mpfr-lib=/usr/local/mpfr-4.1.0/lib

# GCC: https://ftp.gnu.org/gnu/gcc/gcc-4.4.6/
$ ./configure     --disable-multilib     --prefix=/usr/local/gcc-4.4.6   --with-gmp-include=/usr/local/gmp-6.1.0/include --with-gmp-lib=/usr/local/gmp-6.1.0/lib --with-mpfr-include=/usr/local/mpfr-4.1.0/include --with-mpfr-lib=/usr/local/mpfr-4.1.0/lib     --with-mpc-include=/usr/local/mpc-1.2.1/include  --with-mpc-lib=/usr/local/mpc-1.2.1/lib  

# 不设置编译gcc时会报一些不明确的错误
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/gmp-6.1.0/lib:/usr/local/mpfr-4.1.0/lib:/usr/local/mpc-1.2.1/lib

# 编译并安装 需要有zip/jar
$ make && make install
# 卸载
$ make uninstall
```

```bash
$ export PATH=/usr/local/gcc-4.4.6/bin:$PATH
$ export LIBRARY_PATH=/usr/local/gcc-4.4.6/lib64:$LIBRARY_PATH
$ export LD_LIBRARY_PATH=/usr/local/gcc-4.4.6/lib64:/usr/local/gmp-6.1.0/lib:/usr/local/mpfr-4.1.0/lib:/usr/local/mpc-1.2.1/lib:$LD_LIBRARY_PATH
$ export C_INCLUDE_PATH=/usr/local/gcc-4.4.6/include:$C_INCLUDE_PATH
$ export CPLUS_INCLUDE_PATH=/usr/local/gcc-4.4.6/include:$CPLUS_INCLUDE_PATH

# 在oracle pcscfg.cfg（编译似乎会默认从这里找相关依赖文件）中添加 $GCC_PATH
# System default option values taken from: /u01/app/oracle/product/11.2.0/db/precomp/admin/pcscfg.cfg
$export GCC_PATH=/usr/local/gcc-4.4.6/lib/gcc/x86_64-unknown-linux-gnu/4.4.6/include/:/usr/local/gcc-4.4.6/include/c++/4.4.6/tr1,/usr/local/gcc-4.4.6/include/c++/4.4.6:/usr/local/gcc-4.4.6/include/c++/4.4.6/x86_64-unknown-linux-gnu:/usr/local/gcc-4.4.6/libexec/gcc/x86_64-unknown-linux-gnu/4.4.6/cc1plus
```



## 2. GDB 调试

### 语法

```shell
gdb(选项)(参数)
```

### 选项

```shell
-cd：设置工作目录；
-q：安静模式，不打印介绍信息和版本信息；
-d：添加文件查找路径；
-x：从指定文件中执行GDB指令；
-s：设置读取的符号表文件。
-args: 
```

### 常用操作

| 命令                                                       | 解释                                                         | 示例                                                         |
| :--------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| [file](http://man.linuxde.net/file) <文件名>               | 加载被调试的可执行程序文件。 因为一般都在被调试程序所在目录下执行GDB，因而文本名不需要带路径。 | (gdb) file gdb-sample                                        |
| r                                                          | Run的简写，运行被调试的程序。 如果此前没有下过断点，则执行完整个程序；如果有断点，则程序暂停在第一个可用断点处。 | (gdb) r                                                      |
| c                                                          | Continue的简写，继续执行被调试程序，直至下一个断点或程序结束。 | (gdb) c                                                      |
| b <行号> b <函数名称> b *<函数名称> b *<代码地址> d [编号] | b: Breakpoint的简写，设置断点。两可以使用“行号”“函数名称”“执行地址”等方式指定断点位置。 其中在函数名称前面加“*”符号表示将断点设置在“由编译器生成的prolog代码处”。如果不了解汇编，可以不予理会此用法。 d: Delete breakpoint的简写，删除指定编号的某个断点，或删除所有断点。断点编号从1开始递增。 | (gdb) b 8 (gdb) b main (gdb) b *main (gdb) b *0x804835c (gdb) d |
| s, n                                                       | s: 执行一行源程序代码，如果此行代码中有函数调用，则进入该函数； n: 执行一行源程序代码，此行代码中的函数调用也一并执行。 s 相当于其它调试器中的“Step Into (单步跟踪进入)”； n 相当于其它调试器中的“Step Over (单步跟踪)”。 这两个命令必须在有源代码调试信息的情况下才可以使用（GCC编译时使用“-g”参数）。 | (gdb) s (gdb) n                                              |
| si, ni                                                     | si命令类似于s命令，ni命令类似于n命令。所不同的是，这两个命令（si/ni）所针对的是汇编指令，而s/n针对的是源代码。 | (gdb) si (gdb) ni                                            |
| p <变量名称>                                               | Print的简写，显示指定变量（临时变量或全局变量）的值。        | (gdb) p i (gdb) p nGlobalVar                                 |
| display ... undisplay <编号>                               | display，设置程序中断后欲显示的数据及其格式。 例如，如果希望每次程序中断后可以看到即将被执行的下一条汇编指令，可以使用命令 “display /i $pc” 其中 $pc 代表当前汇编指令，/i 表示以十六进行显示。当需要关心汇编代码时，此命令相当有用。 undispaly，取消先前的display设置，编号从1开始递增。 | (gdb) display /i $pc (gdb) undisplay 1                       |
| i                                                          | [info](http://man.linuxde.net/info)的简写，用于显示各类信息，详情请查阅“[help](http://man.linuxde.net/help) i”。 | (gdb) i r                                                    |
| q                                                          | Quit的简写，退出GDB调试环境。                                | (gdb) q                                                      |
| help [命令名称]                                            | GDB帮助命令，提供对GDB名种命令的解释说明。 如果指定了“命令名称”参数，则显示该命令的详细说明；如果没有指定参数，则分类显示所有GDB命令，供用户进一步浏览和查询。 | (gdb) help                                                   |

```
以下内容参考陈皓老师的博客整理，原创博客地址:https://blog.csdn.net/haoel

程序运行参数：
set args 可指定运行时参数（如：set args 10 20 30）
show args 命令可以查看设置好的运行参数。

设置动态库路径
info sharedlibrary  查看so库的加载路径是否正确
可以使用set sysroot、set solib-absolute-prefix、set solib-search-path来指定库搜索路径
1. set sysroot 与 set solib-absolute-prefix 是同一条命令，实际上，set sysroot是set solib-absolute-prefix 的别名。
2. set solib-search-path设置动态库的搜索路径，该命令可设置多个搜索路径，路径之间使用“:”隔开（在linux中为冒号，DOS和Win32中为分号）。
3. set solib-absolute-prefix 与 set solib-search-path 的区别：
在设置了搜索路路径后，最好先用file命令载入主执行文件，再用core命令载入Coredump文件，这样才能保证正确载入库的符号表

断点：
a. 设置断点
break [function] 在进入函数时停住，c++中可以使用class::function或者function(type,type)格式指定函数名。
break [linenum] 在指定行号停住
break +offset/-offset 在当前行号的前面或后面的offset行停住
break [filename:function] 在源文件filename的function函数的入口停住
break *address 在程序运行的内存地址处停住
break 没有参数时，表示在下一条指令处停住
break [...] if [condition] ...可以是上述参数，在条件成立时停住。比如在循环境体中，可以设置break if i=100,标识当i等于100时停住程序
b. 查看断点(n表示断点号)
info break [n]
info breakpoints [n]

观察点：
a. 设置观察点
watch [expr] 为表达式expr设置一个观察点，一旦表达式值有变化，马上停住程序
rwatch [expr] 当表达式expr被读时，停住程序
awatch [expr] 当表达式expr的值被读或被写时停住程序
b. 查看观察点
info watchpoints

捕捉点：
catch [event] event可以是以下类型：
	throw	一个C++抛出的异常
	catch	一个C++捕捉到的异常
	exec	调用系统调用exec时
	fork	调用系统调用fork时。
	vfork	调用系统调用vfork时。
	load/load [libname]	载入共享库时。
	unload/unload [libname]	卸载共享库时
tcatch [event] 只设置一次捕捉点，当程序停住以后，该点被自动删除。

维护停止点：
clear 清楚所有的已定义的停止点
clear [function] 
clear [filename:function] 清除所有设置在函数上的停止点
clear [linenum]
clear [filename:linenum] 清除所有设置在指定行上的停止点
delete/d [breakpoints] [range...] 删除指定断点，breakpoints为断点号。如果不指定默认删除所有断点。range表示范围（如：3-7）。

disable/dis [breakpoints] [range...] disable停止点gdb不会删除，当还需要时，enable即可。
enable [breakpoints] [range...]
enable [breakpoints] once [range...] enable指定的停止点停止后，立马被gdb自动disable
enable [breakpoints] delete [range...] enable指定的停止点停止后，立马被gdb自动删除

停止条件维护：
condition [bnum] [expr] 修改断点号为bnum的停止条件为expr
condition [bnum] 清楚bnum断点的停止条件
ignore [bnum] [count] 忽略bnum断点的停止条件几次

为停止点设定运行命令：
commands [bnum]
... command-list ...
end
为bnum断点设定一个命令列表，当程序被该断点停住时，gdb会依次运行命令列表中的命令。

恢复程序运行和单步调试：
step [count] 单步跟踪，会进入后面调用的函数，执行后面的count条指令
next [count] 单步跟踪，不会进入后续调用的函数，执行后面的count条指令
continue [ignore-count] 
c [ignore-count]
fg [ignore-count] 恢复程序运行，直到程序结束或下个断点到来，ignore-count标识连续忽略后面的断点次数。continue、c、fg一样。
set step-mod on/off  开启或关闭step-mode模式，开启后程序不会因为没有debug信息而不停住。
finish 运行程序直到当前函数完成返回并打印函数返回时的堆栈地址、返回值和参数值等信息。
until/u 运行程序直到退出循环体
stepi/si、nexti/ni 单步跟踪一条机器指令

多线程
info thread 查看当前进程的线程。
thread <ID> 切换调试的线程为指定ID的线程。
break <linespec> thread <threadno>
break <linespec> thread <threadno> if ...
        linespec指定了断点设置在的源程序的行号。threadno指定了线程的ID，注意，这个ID是GDB分配的，你可以通过“info threads”命令来查看正在运行程序中的线程信息。如果你不指定thread <threadno>则表示你的断点设在所有线程上面。你还可以为某线程指定断点条件。如：
	(gdb) break frik.c:13 thread 28 if bartab > lim

break file.c:100 thread all  在file.c文件第100行处为所有经过这里的线程设置断点。
set scheduler-locking off|on|step，这个是问得最多的。在使用step或者continue命令调试当前被调试线程的时候，其他线程也是同时执行的，怎么只让被调试程序执行呢？通过这个命令就可以实现这个需求。
off 不锁定任何线程，也就是所有线程都执行，这是默认值。
on 只有当前被调试程序会执行。
step 在单步的时候，除了next过一个函数的情况(熟悉情况的人可能知道，这其实是一个设置断点然后continue的行为)以外，只有当前线程会执行。
thread apply all bt
	
查看栈信息
backtrace/bt 打印当前函数调用栈所有信息
backtrace/bt [n] 只打印栈顶n层的栈信息
backtrace/bt [-n] 只打印栈底n层的栈信息
a. 如果你要查看某一层的信息，你需要在切换当前的栈，一般来说，程序停止时，最顶层的栈就是当前栈，如果你要查看栈下面层的详细信息，首先要做的是切换当前栈。
frame [n]
f [n] n是一个从0开始的整数，是栈中的层编号。比如：frame 0，表示栈顶，frame 1，表示栈的第二层。
up [n] 表示向栈的上面移动n层，可以不打n，表示向上移动一层。
down [n] 表示向栈的下面移动n层，可以不打n，表示向下移动一层。
          
b. 查看当前栈层的信息，你可以用以下GDB命令：
frame/f 会打印出这些信息：栈的层编号，当前的函数名，函数参数值，函数所在文件及行号，函数执行到的语句。
info frame/info f 会打印出更为详细的当前栈层的信息，只不过，大多数都是运行时的内内地址。比如：函数地址，调用函数的地址，被调用函数的地址，目前的函数是由什么样的程序语言写成的、函数参数地址及值、局部变量的地址等等。
info args 打印出当前函数的参数名及其值。
info locals 打印出当前函数中所有局部变量及其值。
info catch 打印出当前的函数中的异常处理信息。

源代码的内存
info line + 行号/函数名/文件::函数名/文件::行号 查看源代码在内存中的地址
disassemble
disassemble [Function]
disassemble [Address]
disassemble [Start],[End]
disassemble [Function],+[Length]
disassemble [Address],+[Length] 查看源程序当前执行时的机器码

查看运行时数据
print/p [expr]
print /[f] [expr] f是输出的格式
在表达式中，有几种GDB所支持的操作符，它们可以用在任何一种语言中。
	@ 是一个和数组有关的操作符，'@'左边是内存地址的值，右边是想查看内存的长度。如打印vector：
		# 打印多个元素
		print *(your_vector._M_impl._M_start)@your_vector_size
		#打印单个元素[n指下标]
		print *(your_vector._M_impl._M_start+n)
    :: 指定一个在文件或是一个函数中的变量。
    {<type>} <addr> 表示一个指向内存地址<addr>的类型为type的一个对象。
输出格式：
    x  按十六进制格式显示变量。
    d  按十进制格式显示变量。
    u  按十六进制格式显示无符号整型。
    o  按八进制格式显示变量。
    t  按二进制格式显示变量。
    a  按十六进制格式显示变量。
    c  按字符格式显示变量。
    f  按浮点数格式显示变量。
   
程序变量
在GDB中，你可以随时查看以下三种变量的值：
    1、全局变量（所有文件可见的）
    2、静态全局变量（当前文件可见的）
	3、局部变量（当前Scope可见的）
如果你的局部变量和全局变量发生冲突（也就是重名），一般情况下是局部变量会隐藏全局变量，也就是说，如果一个全局变量和一个函数中的局部变量同名时，如果当前停止点在函数中，用print显示出的变量的值会是函数中的局部变量的值。如果此时你想查看全局变量的值时，你可以使用“::”操作符：
    file::variable
    function::variable
可以通过这种形式指定你所想查看的变量，是哪个文件中的或是哪个函数中的。例如，查看文件f2.c中的全局变量x的值：
    gdb) p 'f2.c'::x
当然，“::”操作符会和C++中的发生冲突，GDB能自动识别“::” 是否C++的操作符，所以你不必担心在调试C++程序时会出现异常。  
另外，需要注意的是，如果你的程序编译时开启了优化选项，那么在用GDB调试被优化过的程序时，可能会发生某些变量不能访问，或是取值错误码的情况。这个是很正常的，因为优化程序会删改你的程序，整理你程序的语句顺序，剔除一些无意义的变量等，所以在GDB调试这种程序时，运行时的指令和你所编写指令就有不一样，也就会出现你所想象不到的结果。对付这种情况时，需要在编译程序时关闭编译优化。一般来说，几乎所有的编译器都支持编译优化的开关，例如，GNU的C/C++编译器GCC，你可以使用“-gstabs”选项来解决这个问题。关于编译器的参数，还请查看编译器的使用说明文档。

查看内存
x /[n/f/u] [addr] n、f、u为可选参数
n: 表示显示内存的长度，即从当前地址向后显示几个地址的内容
f: 表示显示的格式，参见上面，如果是字符串，则可以是s，如果是指令地址，可以是i，x, a
u: 表示从当前地址往后请求的字节数，不指定默认4个字节，b表示单字节，h表示双字节，w表示4字节，g表示8字节，当我们指定了字节长度后，GDB会从指定内存地址开始，读取指定字节，并当做一个值取出来
addr 表示内存地址

示例：x/3uh 0x54320表示从内存地址0x54320开始读取内容，h表示以双字节为单位，3表示三个单位，u表示按十六进制显示。 

自动显示
display [expr]
display/[fmt] [expr]
display/[fmt] [addr]
display设定一个或多个表达式后，一旦程序被停住，则gdb会自动显示这些表达式的值。
fmt 表示显示格式，i和s也支持
addr 表示地址
如：display/i $pc $pc是gdb的环境变量，表示指令的地址，/i则表示输出格式为机器指令码，也就是汇编。于是当程序停下后，就会出现源代码和机器指令码相对应的情形。
undisplay [dnums]
delete display[dnums] dnums为设置好了的自动显示的编号。如果要同时删除几个，编号可以用空格分隔，如果删除一个范围内的编号，则可以用减号表示（如：2-5）
disable display [dnums...]
enable display [dnums...] disable和enable不删除自动显示的设置，而只是让其失效和恢复
info display 查看display设置的自动显示的信息。

设置显示选项
set print address on/off 打开或关闭地址输出，当程序显示函数信息时，gdb会显示函数的参数地址，系统默认打开。
show print address 查看打印地址选项是否打开
show print array
set print array on/off 打开数组显示，打开后每个元素占一行，不打开每个元素以逗号分隔，默认关闭。
set print elements [number-of-elements] 指定数组数据显示的最大长度，0表示不限制
show print elements 查看print elements的选项信息
set print null-stop on/off 如果打开了这个选项，当显示字符串的时候，遇到结束符则停止显示。默认关闭。
set print pretty on/off 美化打印
set print sevenbit-strings on/off 设置字符显示，是否按“/nnn”的格式显示，如果打开，则字符串或字符数据按/nnn显示，如”/065“.
set print union on/off 设置显示结构体时，是否显示其内的联合体数据。
set print object on/off 在C++中如果一个对象指针指向其派生类，如果打开这个选项，gdb会自动按虚方法调用的规则显示输出，如果关闭这个选项的话，gdb就不管虚函数表了。默认off
set print static-members on/off 是否显示c++对象中的静态数据成员，默认on
set print vtbl on/off 用比较规整的格式显示虚函数表，默认关闭

gdb环境变量
你可以在GDB的调试环境中定义自己的变量，用来保存一些调试程序中的运行数据。要定义一个GDB的变量很简单只需。使用GDB的set命令。GDB的环境变量和UNIX一样，也是以$起头。如：
	set $foo = *object_ptr
使用环境变量时，GDB会在你第一次使用时创建这个变量，而在以后的使用中，则直接对其賦值。环境变量没有类型，你可以给环境变量定义任一的类型。包括结构体和数组。
show convenience
该命令查看当前所设置的所有的环境变量。这是一个比较强大的功能，环境变量和程序变量的交互使用，将使得程序调试更为灵活便捷。例如：
   set $i = 0
   print bar[$i++]->contents
于是，当你就不必，print bar[0]->contents, print bar[1]->contents地输入命令了。输入这样的命令后，只用敲回车，重复执行上一条语句，环境变量会自动累加，从而完成逐个输出的功能。

查看寄存器
info registers 查看寄存器情况（除了浮点寄存器）
info all-registers 查看所有寄存器情况（包括浮点寄存器）
info registers [regname ...] 查看指定寄存器的情况
寄存器中放置了程序运行时的数据，比如程序当前运行的指令地址（ip），程序的当前堆栈地址（sp）等等。你同样可以使用print命令来访问寄存器的情况，只需要在寄存器名字前加一个$符号就可以了。如：p $eip。

修改变量值
whatis + 变量名 查看变量类型
set var 变量名=值  设置程序的变量值
ptype + 变量类型 查看类型定义

跳转执行
一般来说，被调试程序会按照程序代码的运行顺序依次执行。GDB提供了乱序执行的功能，也就是说，GDB可以修改程序的执行顺序，可以让程序执行随意跳跃。这个功能可以由GDB的jump命令来完：
	jump <linespec> 执行下一条语句的运行点
	jump <address> 跳转到代码行的内存地址	
linespec：可以是文件行号，file:line格式或+num这种偏移量格式
注意，jump命令不会改变当前的程序栈中的内容，所以，当你从一个函数跳到另一个函数时，当函数运行完返回时进行弹栈操作时必然会发生错误，可能结果还是非常奇怪的，甚至于产生程序Core Dump。所以最好是同一个函数中进行跳转。
熟悉汇编的人都知道，程序运行时，有一个寄存器用于保存当前代码所在的内存地址。所以，jump命令也就是改变了这个寄存器中的值。于是，你可以使用“set $pc”来更改跳转执行的地址。如：
   set $pc = 0x485
   
强制函数返回   
return
return <expression>使用return命令取消当前函数的执行，并立即返回，如果指定了<expression>，那么该表达式的值会被认作函数的返回值。   

强制调用函数
 call <expr> 表达式中可以一是函数，以此达到强制调用函数的目的。并显示函数的返回值，如果函数返回值是void，那么就不显示。
另一个相似的命令也可以完成这一功能——print，print后面可以跟表达式，所以也可以用他来调用函数，print和call的不同是，如果函数返回void，call则不显示，print则显示函数返回值，并把该值存入历史数据中。

信号
signal <signal> UNIX的系统信号量通常从1到15。所以<singal>取值也在这个范围。
signal命令和shell的kill命令不同，系统的kill命令发信号给被调试程序时，是由GDB截获的，而signal命令所发出一信号则是直接发给被调试程序的。

信号是一种软中断，是一种处理异步事件的方法。一般来说，操作系统都支持许多信号。尤其是UNIX，比较重要应用程序一般都会处理信号。UNIX定义了许多信号，比如SIGINT表示中断字符信号，也就是Ctrl+C的信号，SIGBUS表示硬件故障的信号；SIGCHLD表示子进程状态改变信号；SIGKILL表示终止程序运行的信号，等等。信号量编程是UNIX下非常重要的一种技术。
GDB有能力在你调试程序的时候处理任何一种信号，你可以告诉GDB需要处理哪一种信号。你可以要求GDB收到你所指定的信号时，马上停住正在运行的程序，以供你进行调试。你可以用GDB的handle命令来完成这一功能。
    handle <signal> <keywords...>
    在GDB中定义一个信号处理。信号<signal>可以以SIG开头或不以SIG开头，可以用定义一个要处理信号的范围（如：SIGIO-SIGKILL，表示处理从SIGIO信号到SIGKILL的信号，其中包括SIGIO，SIGIOT，SIGKILL三个信号），也可以使用关键字all来标明要处理所有的信号。一旦被调试的程序接收到信号，运行程序马上会被GDB停住，以供调试。其<keywords>可以是以下几种关键字的一个或多个。
nostop 当被调试的程序收到信号时，GDB不会停住程序的运行，但会打出消息告诉你收到这种信号。
stop 当被调试的程序收到信号时，GDB会停住你的程序。
print 当被调试的程序收到信号时，GDB会显示出一条信息。
noprint 当被调试的程序收到信号时，GDB不会告诉你收到信号的信息。
pass
noignore 当被调试的程序收到信号时，GDB不处理信号。这表示，GDB会把这个信号交给被调试程序会处理。
nopass
ignore 当被调试的程序收到信号时，GDB不会让被调试程序来处理这个信号。
info signals
info handle 查看有哪些信号在被GDB检测中。

历史记录
当你用GDB的print查看程序运行时的数据时，你每一个print都会被GDB记录下来。GDB会以$1, $2, $3 .....这样的方式为你每一个print命令编上号。于是，你可以使用这个编号访问以前的表达式，如$1。这个功能所带来的好处是，如果你先前输入了一个比较长的表达式，如果你还想查看这个表达式的值，你可以使用历史记录来访问，省去了重复输入。

查看源程序
directory <dirname ... >
dir <dirname ... > 加一个源文件路径到当前路径的前面。如果你要指定多个路径，UNIX下你可以使用“:”，Windows下你可以使用“;”。
directory 清除所有的自定义的源文件搜索路径信息。
show directories 显示定义了的源文件搜索路径。
forward-search <regexp>
search <regexp> 向前面搜索。
reverse-search <regexp> 全部搜索。
list 打印程序源代码
	<linenum>   行号。
    <+offset>   当前行号的正偏移量。
    <-offset>   当前行号的负偏移量。
    <filename:linenum>  哪个文件的哪一行。
    <function>  函数名。
    <filename:function> 哪个文件中的哪个函数。
    <*address>  程序运行时的语句在内存中的地址。
set listsize count 设置显示的行数，默认10
show listsize

在其他语言中使用gdb
show language 查看当前的语言环境。如果GDB不能识为你所调试的编程语言，那么，C语言被认为是默认的环境。
info frame 查看当前函数的程序语言。
info source 查看当前文件的程序语言。
set language 
set language <程序语言名>
```

### STL调试

增加 stl_views.gdb 脚本，内容如下，操作方法：

> * 内容粘贴到.gdbinit或者在.gdbinit中包含该文件
> * 启动gdb之后，用 source stl-views.gdb 把这个脚本包含进来，使用如下命令打印 stl 的内容

| Data type              | GDB command             |
| :--------------------- | :---------------------- |
| std::vector<T>         | pvector *stl_variable*  |
| std::list<T>           | plist *stl_variable* T  |
| std::map<T,T>          | pmap *stl_variable*     |
| std::multimap<T,T>     | pmap *stl_variable*     |
| std::set<T>            | pset *stl_variable* T   |
| std::multiset<T>       | pset *stl_variable*     |
| std::deque<T>          | pdequeue *stl_variable* |
| std::stack<T>          | pstack *stl_variable*   |
| std::queue<T>          | pqueue *stl_variable*   |
| std::priority_queue<T> | ppqueue *stl_variable*  |
| std::bitset<n>td>      | pbitset *stl_variable*  |
| std::string            | pstring *stl_variable*  |
| std::widestring        | pwstring *stl_variable* |

```bash
##########################################
#                                        #
#   STL GDB evaluators/views/utilities   #
#                                        #
##########################################
#
#   The new GDB commands:                                                         
# 	    are entirely non instrumental                                             
# 	    do not depend on any "inline"(s) - e.g. size(), [], etc
#       are extremely tolerant to debugger settings
#                                                                                 
#   This file should be "included" in .gdbinit as following:
#   source stl-views.gdb or just paste it into your .gdbinit file
#
#   The following STL containers are currently supported:
#
#       std::vector<T> -- via pvector command
#       std::list<T> -- via plist command
#       std::map<T,T> -- via pmap command
#       std::multimap<T,T> -- via pmap command
#       std::set<T> -- via pset command
#       std::multiset<T> -- via pset command
#       std::deque<T> -- via pdequeue command
#       std::stack<T> -- via pstack command
#       std::queue<T> -- via pqueue command
#       std::priority_queue<T> -- via ppqueue command
#       std::bitset<n> -- via pbitset command
#       std::string -- via pstring command
#       std::widestring -- via pwstring command
#
#   The end of this file contains (optional) C++ beautifiers
#
##########################################################################
#                                                                        #
# CopyLefty @ 2008 - Dan C Marinescu - No Rights Reserved / GPL V3.      #
# Inspired by intial work of Tom Malnar and many others                 #
#  (do whatever you want with this code, hope it helps)                 #
#   Email: dan_c_marinescu@yahoo.com                                     #
#                                                                        #
##########################################################################



#
# std::vector<>
#

define pvector
	if $argc == 0
		help pvector
	else
		set $size = $arg0._M_impl._M_finish - $arg0._M_impl._M_start
		set $capacity = $arg0._M_impl._M_end_of_storage - $arg0._M_impl._M_start
		set $size_max = $size - 1
	end
	if $argc == 1
		set $i = 0
		while $i < $size
			printf "elem[%u]: ", $i
			p *($arg0._M_impl._M_start + $i)
			set $i++
		end
	end
	if $argc == 2
		set $idx = $arg1
		if $idx < 0 || $idx > $size_max
			printf "idx1, idx2 are not in acceptable range: [0..%u].\n", $size_max
		else
			printf "elem[%u]: ", $idx
			p *($arg0._M_impl._M_start + $idx)
		end
	end
	if $argc == 3
	  set $start_idx = $arg1
	  set $stop_idx = $arg2
	  if $start_idx > $stop_idx
	    set $tmp_idx = $start_idx
	    set $start_idx = $stop_idx
	    set $stop_idx = $tmp_idx
	  end
	  if $start_idx < 0 || $stop_idx < 0 || $start_idx > $size_max || $stop_idx > $size_max
	    printf "idx1, idx2 are not in acceptable range: [0..%u].\n", $size_max
	  else
	    set $i = $start_idx
		while $i <= $stop_idx
			printf "elem[%u]: ", $i
			p *($arg0._M_impl._M_start + $i)
			set $i++
		end
	  end
	end
	if $argc > 0
		printf "Vector size = %u\n", $size
		printf "Vector capacity = %u\n", $capacity
		printf "Element "
		whatis $arg0._M_impl._M_start
	end
end

document pvector
	Prints std::vector<T> information.
	Syntax: pvector <vector> <idx1> <idx2>
	Note: idx, idx1 and idx2 must be in acceptable range [0..<vector>.size()-1].
	Examples:
	pvector v - Prints vector content, size, capacity and T typedef
	pvector v 0 - Prints element[idx] from vector
	pvector v 1 2 - Prints elements in range [idx1..idx2] from vector
end 



#
# std::list<>
#

define plist
	if $argc == 0
		help plist
	else
		set $head = &$arg0._M_impl._M_node
		set $current = $arg0->_M_impl->_M_node->_M_next
		set $size = 0
		while $current != $head
			if $argc == 2
				printf "elem[%u]: ", $size
				p *($arg1*)($current + 1)
			end
			if $argc == 3
				if $size == $arg2
					printf "elem[%u]: ", $size
					p *($arg1*)($current + 1)
				end
			end
			set $current = $current->_M_next
			set $size++
		end
		printf "List size = %u \n", $size
		if $argc == 1
			printf "List "
			whatis $arg0
			printf "Use plist <variable_name> <element_type> to see the elements in the list.\n"
		end
	end
end

document plist
	Prints std::list<T> information.
	Syntax: plist <list> <T> <idx>: Prints list size, if T defined all elements or just element at idx
	Examples:
	plist l - prints list size and definition
	plist l int - prints all elements and list size
	plist l int 2 - prints the third element in the list (if exists) and list size
end



#
# std::map and std::multimap
#

define pmap
	if $argc == 0
		help pmap
	else
		set $tree = $arg0
		set $i = 0
		set $node = $tree->_M_t->_M_impl->_M_header->_M_left
		set $end = $tree->_M_t->_M_impl->_M_header
		set $tree_size = $tree->_M_t->_M_impl->_M_node_count
		if $argc == 1
			printf "Map "
			whatis $tree
			printf "Use pmap <variable_name> <left_element_type> <right_element_type> to see the elements in the map.\n"
		end
		if $argc == 3
			while $i < $tree_size
				set $value = (void *)($node + 1)
				printf "elem[%u]->left: ", $i
				p *($arg1*)$value
				set $value = $value + 4
				printf "elem[%u]->right: ", $i
				p *($arg2*)$value
				if $node->_M_right != 0
					set $node = $node->_M_right
					while $node->_M_left != 0
						set $node = $node->_M_left
					end
				else
					set $tmp_node = $node->_M_parent
					while $node == $tmp_node->_M_right
						set $node = $tmp_node
						set $tmp_node = $tmp_node->_M_parent
					end
					if $node->_M_right != $tmp_node
						set $node = $tmp_node
					end
				end
				set $i++
			end
		end
		if $argc == 4
			set $idx = $arg3
			set $ElementsFound = 0
			while $i < $tree_size
				set $value = (void *)($node + 1)
				if *($arg1*)$value == $idx
					printf "elem[%u]->left: ", $i
					p *($arg1*)$value
					set $value = $value + 4
					printf "elem[%u]->right: ", $i
					p *($arg2*)$value
					set $ElementsFound++
				end
				if $node->_M_right != 0
					set $node = $node->_M_right
					while $node->_M_left != 0
						set $node = $node->_M_left
					end
				else
					set $tmp_node = $node->_M_parent
					while $node == $tmp_node->_M_right
						set $node = $tmp_node
						set $tmp_node = $tmp_node->_M_parent
					end
					if $node->_M_right != $tmp_node
						set $node = $tmp_node
					end
				end
				set $i++
			end
			printf "Number of elements found = %u\n", $ElementsFound
		end
		if $argc == 5
			set $idx1 = $arg3
			set $idx2 = $arg4
			set $ElementsFound = 0
			while $i < $tree_size
				set $value = (void *)($node + 1)
				set $valueLeft = *($arg1*)$value
				set $valueRight = *($arg2*)($value + 4)
				if $valueLeft == $idx1 && $valueRight == $idx2
					printf "elem[%u]->left: ", $i
					p $valueLeft
					printf "elem[%u]->right: ", $i
					p $valueRight
					set $ElementsFound++
				end
				if $node->_M_right != 0
					set $node = $node->_M_right
					while $node->_M_left != 0
						set $node = $node->_M_left
					end
				else
					set $tmp_node = $node->_M_parent
					while $node == $tmp_node->_M_right
						set $node = $tmp_node
						set $tmp_node = $tmp_node->_M_parent
					end
					if $node->_M_right != $tmp_node
						set $node = $tmp_node
					end
				end
				set $i++
			end
			printf "Number of elements found = %u\n", $ElementsFound
		end
		printf "Map size = %u\n", $tree_size
	end
end

document pmap
	Prints std::map<TLeft and TRight> or std::multimap<TLeft and TRight> information. Works for std::multimap as well.
	Syntax: pmap <map> <TtypeLeft> <TypeRight> <valLeft> <valRight>: Prints map size, if T defined all elements or just element(s) with val(s)
	Examples:
	pmap m - prints map size and definition
	pmap m int int - prints all elements and map size
	pmap m int int 20 - prints the element(s) with left-value = 20 (if any) and map size
	pmap m int int 20 200 - prints the element(s) with left-value = 20 and right-value = 200 (if any) and map size
end



#
# std::set and std::multiset
#

define pset
	if $argc == 0
		help pset
	else
		set $tree = $arg0
		set $i = 0
		set $node = $tree->_M_t->_M_impl->_M_header->_M_left
		set $end = $tree->_M_t->_M_impl->_M_header
		set $tree_size = $tree->_M_t->_M_impl->_M_node_count
		if $argc == 1
			printf "Set "
			whatis $tree
			printf "Use pset <variable_name> <element_type> to see the elements in the set.\n"
		end
		if $argc == 2
			while $i < $tree_size
				set $value = (void *)($node + 1)
				printf "elem[%u]: ", $i
				p *($arg1*)$value
				if $node->_M_right != 0
					set $node = $node->_M_right
					while $node->_M_left != 0
						set $node = $node->_M_left
					end
				else
					set $tmp_node = $node->_M_parent
					while $node == $tmp_node->_M_right
						set $node = $tmp_node
						set $tmp_node = $tmp_node->_M_parent
					end
					if $node->_M_right != $tmp_node
						set $node = $tmp_node
					end
				end
				set $i++
			end
		end
		if $argc == 3
			set $idx = $arg2
			set $ElementsFound = 0
			while $i < $tree_size
				set $value = (void *)($node + 1)
				if *($arg1*)$value == $idx
					printf "elem[%u]: ", $i
					p *($arg1*)$value
					set $ElementsFound++
				end
				if $node->_M_right != 0
					set $node = $node->_M_right
					while $node->_M_left != 0
						set $node = $node->_M_left
					end
				else
					set $tmp_node = $node->_M_parent
					while $node == $tmp_node->_M_right
						set $node = $tmp_node
						set $tmp_node = $tmp_node->_M_parent
					end
					if $node->_M_right != $tmp_node
						set $node = $tmp_node
					end
				end
				set $i++
			end
			printf "Number of elements found = %u\n", $ElementsFound
		end
		printf "Set size = %u\n", $tree_size
	end
end

document pset
	Prints std::set<T> or std::multiset<T> information. Works for std::multiset as well.
	Syntax: pset <set> <T> <val>: Prints set size, if T defined all elements or just element(s) having val
	Examples:
	pset s - prints set size and definition
	pset s int - prints all elements and the size of s
	pset s int 20 - prints the element(s) with value = 20 (if any) and the size of s
end



#
# std::dequeue
#

define pdequeue
	if $argc == 0
		help pdequeue
	else
		set $size = 0
		set $start_cur = $arg0._M_impl._M_start._M_cur
		set $start_last = $arg0._M_impl._M_start._M_last
		set $start_stop = $start_last
		while $start_cur != $start_stop
			p *$start_cur
			set $start_cur++
			set $size++
		end
		set $finish_first = $arg0._M_impl._M_finish._M_first
		set $finish_cur = $arg0._M_impl._M_finish._M_cur
		set $finish_last = $arg0._M_impl._M_finish._M_last
		if $finish_cur < $finish_last
			set $finish_stop = $finish_cur
		else
			set $finish_stop = $finish_last
		end
		while $finish_first != $finish_stop
			p *$finish_first
			set $finish_first++
			set $size++
		end
		printf "Dequeue size = %u\n", $size
	end
end

document pdequeue
	Prints std::dequeue<T> information.
	Syntax: pdequeue <dequeue>: Prints dequeue size, if T defined all elements
	Deque elements are listed "left to right" (left-most stands for front and right-most stands for back)
	Example:
	pdequeue d - prints all elements and size of d
end



#
# std::stack
#

define pstack
	if $argc == 0
		help pstack
	else
		set $start_cur = $arg0.c._M_impl._M_start._M_cur
		set $finish_cur = $arg0.c._M_impl._M_finish._M_cur
		set $size = $finish_cur - $start_cur
        set $i = $size - 1
        while $i >= 0
            p *($start_cur + $i)
            set $i--
        end
		printf "Stack size = %u\n", $size
	end
end

document pstack
	Prints std::stack<T> information.
	Syntax: pstack <stack>: Prints all elements and size of the stack
	Stack elements are listed "top to buttom" (top-most element is the first to come on pop)
	Example:
	pstack s - prints all elements and the size of s
end



#
# std::queue
#

define pqueue
	if $argc == 0
		help pqueue
	else
		set $start_cur = $arg0.c._M_impl._M_start._M_cur
		set $finish_cur = $arg0.c._M_impl._M_finish._M_cur
		set $size = $finish_cur - $start_cur
        set $i = 0
        while $i < $size
            p *($start_cur + $i)
            set $i++
        end
		printf "Queue size = %u\n", $size
	end
end

document pqueue
	Prints std::queue<T> information.
	Syntax: pqueue <queue>: Prints all elements and the size of the queue
	Queue elements are listed "top to bottom" (top-most element is the first to come on pop)
	Example:
	pqueue q - prints all elements and the size of q
end



#
# std::priority_queue
#

define ppqueue
	if $argc == 0
		help ppqueue
	else
		set $size = $arg0.c._M_impl._M_finish - $arg0.c._M_impl._M_start
		set $capacity = $arg0.c._M_impl._M_end_of_storage - $arg0.c._M_impl._M_start
		set $i = $size - 1
		while $i >= 0
			p *($arg0.c._M_impl._M_start + $i)
			set $i--
		end
		printf "Priority queue size = %u\n", $size
		printf "Priority queue capacity = %u\n", $capacity
	end
end

document ppqueue
	Prints std::priority_queue<T> information.
	Syntax: ppqueue <priority_queue>: Prints all elements, size and capacity of the priority_queue
	Priority_queue elements are listed "top to buttom" (top-most element is the first to come on pop)
	Example:
	ppqueue pq - prints all elements, size and capacity of pq
end



#
# std::bitset
#

define pbitset
	if $argc == 0
		help pbitset
	else
        p /t $arg0._M_w
	end
end

document pbitset
	Prints std::bitset<n> information.
	Syntax: pbitset <bitset>: Prints all bits in bitset
	Example:
	pbitset b - prints all bits in b
end



#
# std::string
#

define pstring
	if $argc == 0
		help pstring
	else
		printf "String \t\t\t= \"%s\"\n", $arg0._M_data()
		printf "String size/length \t= %u\n", $arg0._M_rep()->_M_length
		printf "String capacity \t= %u\n", $arg0._M_rep()->_M_capacity
		printf "String ref-count \t= %d\n", $arg0._M_rep()->_M_refcount
	end
end

document pstring
	Prints std::string information.
	Syntax: pstring <string>
	Example:
	pstring s - Prints content, size/length, capacity and ref-count of string s
end 



#
# std::wstring
#

define pwstring
	if $argc == 0
		help pwstring
	else
		call printf("WString \t\t= \"%ls\"\n", $arg0._M_data())
		printf "WString size/length \t= %u\n", $arg0._M_rep()->_M_length
		printf "WString capacity \t= %u\n", $arg0._M_rep()->_M_capacity
		printf "WString ref-count \t= %d\n", $arg0._M_rep()->_M_refcount
	end
end

document pwstring
	Prints std::wstring information.
	Syntax: pwstring <wstring>
	Example:
	pwstring s - Prints content, size/length, capacity and ref-count of wstring s
end 

#
# C++ related beautifiers
#

set print pretty on
set print object on
set print static-members on
set print vtbl on
set print demangle on
set demangle-style gnu-v3
set print sevenbit-strings off
```

### 多线程

```
info threads 查看当前进程的线程
thread <ID>  切换调试的线程为指定ID的线程
break test.c:100 thread all   在所有线程中相应的行上设置断点

set scheduler-locking off|on|step
  off   默认值，不锁定任何线程，所有线程都执行
  on    只有当前被调试程序会执行
  step  表示在单步执行的时候，只有当前线程会执行；
```

### 多进程

```
单独调试子进程：
用attach命令将子进程的ID附加到gdb调试器上；

使用调试器选项follow-fork-mode
gdb调试器的选项follow-fork-mode允许我们选择程序在执行了fork调用后继续执行父进程还是子进程
set follow-fork-mode mode mode选项值可以是parent和child;该命令可以进入到子进程或者父进程；
```

### gdb dump

```shell
(gdb) dump memory ./dump.bin 0x7f4f64000000 7f4f68000000

# 找到dump出来的文件
$ strings dump.bin
```



###  问题

#### 打印出的字符串内容不全

```bash
(gdb) show print elements    #查看最大打印字符数
(gdb) set print elements 20000 #设置为20000
```

## 3. Makefile

### 编译选项

### 常用变量

#### 自动化变量

```makefile
$@ 表示规则中的目标文件集。在模式规则中，如果有多个目标，那么，"$@"就是匹配于目标中模式定义的集合。
$% 仅当目标是函数库文件中，表示规则中的目标成员名。例如，如果一个目标是"foo.a(bar.o)"，那么，"$%"就是"bar.o"，"$@"就是"foo.a"。如果目标不是函数库文件（Unix下是[.a]，Windows下是[.lib]），那么，其值为空。
$< 依赖目标中的第一个目标名字。如果依赖目标是以模式（即"%"）定义的，那么"$<"将是符合模式的一系列的文件集。注意，其是一个一个取出来的。
$? 所有比目标新的依赖目标的集合。以空格分隔。
$^ 所有的依赖目标的集合。以空格分隔。如果在依赖目标中有多个重复的，那个这个变量会去除重复的依赖目标，只保留一份。
$+ 这个变量很像"$^"，也是所有依赖目标的集合。只是它不去除重复的依赖目标。
$* 这个变量表示目标模式中"%"及其之前的部分。如果目标是"dir/a.foo.b"，并且目标的模式是"a.%.b"，那么，"$*"的值就是"dir/a.foo"。这个变量对于构造有关联的文件名是比较有较。如果目标中没有模式的定义，那么"$*"也就不能被推导出，但是，如果目标文件的后缀是make所识别的，那么"$*"就是除了后缀的那一部分。例如：如果目标是"foo.c"，因为".c"是make所能识别的后缀名，所以，"$*"的值就是"foo"。这个特性是GNU make的，很有可能不兼容于其它版本的make，所以，你应该尽量避免使用"$*"，除非是在隐含规则或是静态模式中。如果目标中的后缀是make所不能识别的，那么"$*"就是空值。

下面是对于上面的七个变量分别加上"D"或是"F"的含义：
$(@D) 表示"$@"的目录部分（不以斜杠作为结尾），如果"$@"值是"dir/foo.o"，那么"$(@D)"就是"dir"，而如果"$@"中没有包含斜杠的话，其值就是"."（当前目录）。
$(@F) 表示"$@"的文件部分，如果"$@"值是"dir/foo.o"，那么"$(@F)"就是"foo.o"，"$(@F)"相当于函数"$(notdir $@)"。
"$(*D)"
"$(*F)" 和上面所述的同理，也是取文件的目录部分和文件部分。对于上面的那个例子，"$(*D)"返回"dir"，而"$(*F)"返回"foo"
"$(%D)"
"$(%F)" 分别表示了函数包文件成员的目录部分和文件部分。这对于形同"archive(member)"形式的目标中的"member"中包含了不同的目录很有用。
"$(<D)"
"$(<F)" 分别表示依赖文件的目录部分和文件部分。
"$(^D)"
"$(^F)" 分别表示所有依赖文件的目录部分和文件部分。（无相同的）
"$(+D)"
"$(+F)" 分别表示所有依赖文件的目录部分和文件部分。（可以有相同的）
"$(?D)"
"$(?F)" 分别表示被更新的依赖文件的目录部分和文件部分。
最后想提醒一下的是，对于"$<"，为了避免产生不必要的麻烦，我们最好给$后面的那个特定字符都加上圆括号，比如，"$(<)"就要比"$<"要好一些。
```



## 4. CMake

### 常用变量

| 变量                           | 说明                         |
| ------------------------------ | ---------------------------- |
|                                |                              |
| CMAKE_CURRRENT_BINARY_DIR      |                              |
| CMAKE_CURRENT_SOURCE_DIR       |                              |
| CMAKE_BINARY_DIR               |                              |
| CMAKE_SOURCE_DIR               |                              |
| CMAKE_VERBOSE_MAKEFILE         | 启用Makefile版本中的详细输出 |
| CMAKE_ARCHIVE_OUTPUT_DIRECTORY | 静态库输出路径               |
| CMAKE_LIBRARY_OUTPUT_DIRECTORY | 动态库输出路径               |
| CMAKE_RUNTIME_OUTPUT_DIRECTORY | 可执行文件输出路径           |
|                                |                              |

### 操作说明

| 操作                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| add_library(<name> [STATIC \|SHARED \|MODULE] [EXCLUDE_FROM_ALL] [source1] [source2 ...]) | 生成库文件。如果指定了`STATIC`，就是生成静态库；如果指定了`SHARED`，就是生成动态库；如果指定了`MODULE`，就是使用类dl-open函数加载的动态库； |
| aux_source_directory(<dir> <variable>)                       | 收集指定目录中所有**源文件**的名称，并将列表存储在提供的<variable>变量中 |
| link_directories（<dir>）                                    | 指定库文件的路径                                             |
| include_directories(<dir>)                                   | 指定头文件的路径                                             |
| add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL]) | 将一个子目录添加到构建中                                     |
| target_link_libraries                                        |                                                              |
| add_dependencies                                             | add_dependencies可以在直接编译上层target时，自动检查下层依赖库是否已经生成。没有的话先编译下层依赖库，然后再编译上层target，最后link depend target。 |
| link_libraries([item1 [item2 [...]]]                [[debug\|optimized\|general] <item>] ...) |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |

```cmake
# CMakeList.txt: demo 的 CMake 项目，在此处包括源代码并定义
# 项目特定的逻辑。
#
cmake_minimum_required (VERSION 3.8)

project ("demo")

# 将源代码添加到此项目的可执行文件。
# add_executable (demo "demo.cpp" "demo.h")

# TODO: 如有需要，请添加测试并安装目标。
# 添加头文件目录
include_directories(../../include)
# 添加需要链接的库文件目录
link_directories(../../lib)

# 添加 math 子目录
# add_subdirectory(asio)

# 设置
# set(CMAKE_CXX_STANDARD 11)

# 指定生成目标 
# add_executable(${PROJECT_NAME}.out asio/timer01.cpp)

# 指定可执行文件和lib文件目录
set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin )
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib )

# If necessary, use the RELATIVE flag, otherwise each source file may be listed 
# with full pathname. RELATIVE may makes it easier to extract an executable name
# automatically.
# file( GLOB APP_SOURCES RELATIVE app/*.cxx )
# get_filename_component(<var> <FileName> <mode> [CACHE])

file( GLOB APP_SOURCES RELATIVE_PATH *.cpp test_server/*.cpp)
foreach( testsourcefile ${APP_SOURCES} )
    # I used a simple string replace, to cut off .cpp.
    # string( REPLACE ".cpp" "" testname ${testsourcefile} )
    get_filename_component(testname ${testsourcefile} NAME_WE)
    add_executable( ${testname} ${testsourcefile} )
    # Make sure YourLib is linked to each app
    # 添加链接库
    target_link_libraries(${testname} libevent ws2_32)
    # target_link_libraries(${testname}.exe boost_chrono)
endforeach( testsourcefile ${APP_SOURCES} )
```

### 建议

#### 新建build目录执行cmake构建

有时候为了不让cmake生成的文件污染我们的目录，我们可以在项目中新建一个build目录，然后在build目录中执行cmake构建，如下：

![image-20201011234326118](images/tool/image-20201011234326118.png)

```bash
$ cd build
$ cmake ../
```



### solution

#### Cmake Parse error. Expected a command name, got unquoted argument with text “ ”

utf8编码的bug： vim 打开 ：

```bash
$ :set nobomb
```

## 5. pmap

根据pmap查出大内存块的信息

```bash
$ pmap -X 3707 > mem.txt

$ cat mem.txt | awk '{print $3}' | sed '1,2d' | uniq -c | sort -nr

$ cat mem.txt | sed -n '1, 2p' > st_mem.txt
$ cat mem.txt | sed '1, 2d' | sed '$d' | sed '$d' | sort -nrk 7 >> st_mem.txt
$ cat mem.txt | sed '$d' | sed -n '$p' >> st_mem.txt
$ cat mem.txt | sed -n '$p' >> st_mem.txt
```

### /proc/<pid>/maps

结合上面分析出的大内存块查找内存地址。

```bash
$ cat /proc/<pid>/maps 
```

## 6. valgrind

```
VALGRIND(1)                                Release 3.15.0                                VALGRIND(1)

NAME
       valgrind - a suite of tools for debugging and profiling programs

SYNOPSIS
       valgrind [valgrind-options] [your-program] [your-program-options]

DESCRIPTION
       Valgrind is a flexible program for debugging and profiling Linux executables. It consists of
       a core, which provides a synthetic CPU in software, and a series of debugging and profiling
       tools. The architecture is modular, so that new tools can be created easily and without
       disturbing the existing structure.

       Some of the options described below work with all Valgrind tools, and some only work with a
       few or one. The section MEMCHECK OPTIONS and those below it describe tool-specific options.

       This manual page covers only basic usage and options. For more comprehensive information,
       please see the HTML documentation on your system:
       $INSTALL/share/doc/valgrind/html/index.html, or online:
       http://www.valgrind.org/docs/manual/index.html.

TOOL SELECTION OPTIONS
       The single most important option.

       --tool=<toolname> [default: memcheck]
           Run the Valgrind tool called toolname, e.g. memcheck, cachegrind, callgrind, helgrind,
           drd, massif, dhat, lackey, none, exp-sgcheck, exp-bbv, etc.

BASIC OPTIONS
       These options work with all tools.

       -h --help
           Show help for all options, both for the core and for the selected tool. If the option is
           repeated it is equivalent to giving --help-debug.

       --help-debug
           Same as --help, but also lists debugging options which usually are only of use to
           Valgrind's developers.

       --version
           Show the version number of the Valgrind core. Tools can have their own version numbers.
           There is a scheme in place to ensure that tools only execute when the core version is one
           they are known to work with. This was done to minimise the chances of strange problems
           arising from tool-vs-core version incompatibilities.

       -q, --quiet
           Run silently, and only print error messages. Useful if you are running regression tests
           or have some other automated test machinery.

       -v, --verbose
           Be more verbose. Gives extra information on various aspects of your program, such as: the
           shared objects loaded, the suppressions used, the progress of the instrumentation and
           execution engines, and warnings about unusual behaviour. Repeating the option increases
           the verbosity level.

       --trace-children=<yes|no> [default: no]
           When enabled, Valgrind will trace into sub-processes initiated via the exec system call.
           This is necessary for multi-process programs.

           Note that Valgrind does trace into the child of a fork (it would be difficult not to,
           since fork makes an identical copy of a process), so this option is arguably badly named.
           However, most children of fork calls immediately call exec anyway.

       --trace-children-skip=patt1,patt2,...
           This option only has an effect when --trace-children=yes is specified. It allows for some
           children to be skipped. The option takes a comma separated list of patterns for the names
           of child executables that Valgrind should not trace into. Patterns may include the
           metacharacters ?  and *, which have the usual meaning.

           This can be useful for pruning uninteresting branches from a tree of processes being run
           on Valgrind. But you should be careful when using it. When Valgrind skips tracing into an
           executable, it doesn't just skip tracing that executable, it also skips tracing any of
           that executable's child processes. In other words, the flag doesn't merely cause tracing
           to stop at the specified executables -- it skips tracing of entire process subtrees
           rooted at any of the specified executables.

       --trace-children-skip-by-arg=patt1,patt2,...
           This is the same as --trace-children-skip, with one difference: the decision as to
           whether to trace into a child process is made by examining the arguments to the child
           process, rather than the name of its executable.

       --child-silent-after-fork=<yes|no> [default: no]
           When enabled, Valgrind will not show any debugging or logging output for the child
           process resulting from a fork call. This can make the output less confusing (although
           more misleading) when dealing with processes that create children. It is particularly
           useful in conjunction with --trace-children=. Use of this option is also strongly
           recommended if you are requesting XML output (--xml=yes), since otherwise the XML from
           child and parent may become mixed up, which usually makes it useless.

       --vgdb=<no|yes|full> [default: yes]
           Valgrind will provide "gdbserver" functionality when --vgdb=yes or --vgdb=full is
           specified. This allows an external GNU GDB debugger to control and debug your program
           when it runs on Valgrind.  --vgdb=full incurs significant performance overheads, but
           provides more precise breakpoints and watchpoints. See Debugging your program using
           Valgrind's gdbserver and GDB for a detailed description.

           If the embedded gdbserver is enabled but no gdb is currently being used, the vgdb command
           line utility can send "monitor commands" to Valgrind from a shell. The Valgrind core
           provides a set of Valgrind monitor commands. A tool can optionally provide tool specific
           monitor commands, which are documented in the tool specific chapter.

       --vgdb-error=<number> [default: 999999999]
           Use this option when the Valgrind gdbserver is enabled with --vgdb=yes or --vgdb=full.
           Tools that report errors will wait for "number" errors to be reported before freezing the
           program and waiting for you to connect with GDB. It follows that a value of zero will
           cause the gdbserver to be started before your program is executed. This is typically used
           to insert GDB breakpoints before execution, and also works with tools that do not report
           errors, such as Massif.

       --vgdb-stop-at=<set> [default: none]
           Use this option when the Valgrind gdbserver is enabled with --vgdb=yes or --vgdb=full.
           The Valgrind gdbserver will be invoked for each error after --vgdb-error have been
           reported. You can additionally ask the Valgrind gdbserver to be invoked for other events,
           specified in one of the following ways:

           ·   a comma separated list of one or more of startup exit valgrindabexit.

               The values startup exit valgrindabexit respectively indicate to invoke gdbserver
               before your program is executed, after the last instruction of your program, on
               Valgrind abnormal exit (e.g. internal error, out of memory, ...).

               Note: startup and --vgdb-error=0 will both cause Valgrind gdbserver to be invoked
               before your program is executed. The --vgdb-error=0 will in addition cause your
               program to stop on all subsequent errors.

           ·   all to specify the complete set. It is equivalent to
               --vgdb-stop-at=startup,exit,valgrindabexit.

           ·   none for the empty set.

       --track-fds=<yes|no> [default: no]
           When enabled, Valgrind will print out a list of open file descriptors on exit or on
           request, via the gdbserver monitor command v.info open_fds. Along with each file
           descriptor is printed a stack backtrace of where the file was opened and any details
           relating to the file descriptor such as the file name or socket details.

       --time-stamp=<yes|no> [default: no]
           When enabled, each message is preceded with an indication of the elapsed wallclock time
           since startup, expressed as days, hours, minutes, seconds and milliseconds.

       --log-fd=<number> [default: 2, stderr]
           Specifies that Valgrind should send all of its messages to the specified file descriptor.
           The default, 2, is the standard error channel (stderr). Note that this may interfere with
           the client's own use of stderr, as Valgrind's output will be interleaved with any output
           that the client sends to stderr.

       --log-file=<filename>
           Specifies that Valgrind should send all of its messages to the specified file. If the
           file name is empty, it causes an abort. There are three special format specifiers that
           can be used in the file name.

           %p is replaced with the current process ID. This is very useful for program that invoke
           multiple processes. WARNING: If you use --trace-children=yes and your program invokes
           multiple processes OR your program forks without calling exec afterwards, and you don't
           use this specifier (or the %q specifier below), the Valgrind output from all those
           processes will go into one file, possibly jumbled up, and possibly incomplete. Note: If
           the program forks and calls exec afterwards, Valgrind output of the child from the period
           between fork and exec will be lost. Fortunately this gap is really tiny for most
           programs; and modern programs use posix_spawn anyway.

           %n is replaced with a file sequence number unique for this process. This is useful for
           processes that produces several files from the same filename template.

           %q{FOO} is replaced with the contents of the environment variable FOO. If the {FOO} part
           is malformed, it causes an abort. This specifier is rarely needed, but very useful in
           certain circumstances (eg. when running MPI programs). The idea is that you specify a
           variable which will be set differently for each process in the job, for example
           BPROC_RANK or whatever is applicable in your MPI setup. If the named environment variable
           is not set, it causes an abort. Note that in some shells, the { and } characters may need
           to be escaped with a backslash.

           %% is replaced with %.

           If an % is followed by any other character, it causes an abort.

           If the file name specifies a relative file name, it is put in the program's initial
           working directory: this is the current directory when the program started its execution
           after the fork or after the exec. If it specifies an absolute file name (ie. starts with
           '/') then it is put there.

       --log-socket=<ip-address:port-number>
           Specifies that Valgrind should send all of its messages to the specified port at the
           specified IP address. The port may be omitted, in which case port 1500 is used. If a
           connection cannot be made to the specified socket, Valgrind falls back to writing output
           to the standard error (stderr). This option is intended to be used in conjunction with
           the valgrind-listener program. For further details, see the commentary in the manual.

ERROR-RELATED OPTIONS
       These options are used by all tools that can report errors, e.g. Memcheck, but not
       Cachegrind.

       --xml=<yes|no> [default: no]
           When enabled, the important parts of the output (e.g. tool error messages) will be in XML
           format rather than plain text. Furthermore, the XML output will be sent to a different
           output channel than the plain text output. Therefore, you also must use one of --xml-fd,
           --xml-file or --xml-socket to specify where the XML is to be sent.

           Less important messages will still be printed in plain text, but because the XML output
           and plain text output are sent to different output channels (the destination of the plain
           text output is still controlled by --log-fd, --log-file and --log-socket) this should not
           cause problems.

           This option is aimed at making life easier for tools that consume Valgrind's output as
           input, such as GUI front ends. Currently this option works with Memcheck, Helgrind, DRD
           and SGcheck. The output format is specified in the file
           docs/internals/xml-output-protocol4.txt in the source tree for Valgrind 3.5.0 or later.

           The recommended options for a GUI to pass, when requesting XML output, are: --xml=yes to
           enable XML output, --xml-file to send the XML output to a (presumably GUI-selected) file,
           --log-file to send the plain text output to a second GUI-selected file,
           --child-silent-after-fork=yes, and -q to restrict the plain text output to critical error
           messages created by Valgrind itself. For example, failure to read a specified
           suppressions file counts as a critical error message. In this way, for a successful run
           the text output file will be empty. But if it isn't empty, then it will contain important
           information which the GUI user should be made aware of.

       --xml-fd=<number> [default: -1, disabled]
           Specifies that Valgrind should send its XML output to the specified file descriptor. It
           must be used in conjunction with --xml=yes.

       --xml-file=<filename>
           Specifies that Valgrind should send its XML output to the specified file. It must be used
           in conjunction with --xml=yes. Any %p or %q sequences appearing in the filename are
           expanded in exactly the same way as they are for --log-file. See the description of
           --log-file for details.

       --xml-socket=<ip-address:port-number>
           Specifies that Valgrind should send its XML output the specified port at the specified IP
           address. It must be used in conjunction with --xml=yes. The form of the argument is the
           same as that used by --log-socket. See the description of --log-socket for further
           details.

       --xml-user-comment=<string>
           Embeds an extra user comment string at the start of the XML output. Only works when
           --xml=yes is specified; ignored otherwise.

       --demangle=<yes|no> [default: yes]
           Enable/disable automatic demangling (decoding) of C++ names. Enabled by default. When
           enabled, Valgrind will attempt to translate encoded C++ names back to something
           approaching the original. The demangler handles symbols mangled by g++ versions 2.X, 3.X
           and 4.X.

           An important fact about demangling is that function names mentioned in suppressions files
           should be in their mangled form. Valgrind does not demangle function names when searching
           for applicable suppressions, because to do otherwise would make suppression file contents
           dependent on the state of Valgrind's demangling machinery, and also slow down suppression
           matching.

       --num-callers=<number> [default: 12]
           Specifies the maximum number of entries shown in stack traces that identify program
           locations. Note that errors are commoned up using only the top four function locations
           (the place in the current function, and that of its three immediate callers). So this
           doesn't affect the total number of errors reported.

           The maximum value for this is 500. Note that higher settings will make Valgrind run a bit
           more slowly and take a bit more memory, but can be useful when working with programs with
           deeply-nested call chains.

       --unw-stack-scan-thresh=<number> [default: 0] , --unw-stack-scan-frames=<number> [default: 5]
           Stack-scanning support is available only on ARM targets.

           These flags enable and control stack unwinding by stack scanning. When the normal stack
           unwinding mechanisms -- usage of Dwarf CFI records, and frame-pointer following -- fail,
           stack scanning may be able to recover a stack trace.

           Note that stack scanning is an imprecise, heuristic mechanism that may give very
           misleading results, or none at all. It should be used only in emergencies, when normal
           unwinding fails, and it is important to nevertheless have stack traces.

           Stack scanning is a simple technique: the unwinder reads words from the stack, and tries
           to guess which of them might be return addresses, by checking to see if they point just
           after ARM or Thumb call instructions. If so, the word is added to the backtrace.

           The main danger occurs when a function call returns, leaving its return address exposed,
           and a new function is called, but the new function does not overwrite the old address.
           The result of this is that the backtrace may contain entries for functions which have
           already returned, and so be very confusing.

           A second limitation of this implementation is that it will scan only the page (4KB,
           normally) containing the starting stack pointer. If the stack frames are large, this may
           result in only a few (or not even any) being present in the trace. Also, if you are
           unlucky and have an initial stack pointer near the end of its containing page, the scan
           may miss all interesting frames.

           By default stack scanning is disabled. The normal use case is to ask for it when a stack
           trace would otherwise be very short. So, to enable it, use
           --unw-stack-scan-thresh=number. This requests Valgrind to try using stack scanning to
           "extend" stack traces which contain fewer than number frames.

           If stack scanning does take place, it will only generate at most the number of frames
           specified by --unw-stack-scan-frames. Typically, stack scanning generates so many garbage
           entries that this value is set to a low value (5) by default. In no case will a stack
           trace larger than the value specified by --num-callers be created.

       --error-limit=<yes|no> [default: yes]
           When enabled, Valgrind stops reporting errors after 10,000,000 in total, or 1,000
           different ones, have been seen. This is to stop the error tracking machinery from
           becoming a huge performance overhead in programs with many errors.

       --error-exitcode=<number> [default: 0]
           Specifies an alternative exit code to return if Valgrind reported any errors in the run.
           When set to the default value (zero), the return value from Valgrind will always be the
           return value of the process being simulated. When set to a nonzero value, that value is
           returned instead, if Valgrind detects any errors. This is useful for using Valgrind as
           part of an automated test suite, since it makes it easy to detect test cases for which
           Valgrind has reported errors, just by inspecting return codes.

       --exit-on-first-error=<yes|no> [default: no]
           If this option is enabled, Valgrind exits on the first error. A nonzero exit value must
           be defined using --error-exitcode option. Useful if you are running regression tests or
           have some other automated test machinery.

       --error-markers=<begin>,<end> [default: none]
           When errors are output as plain text (i.e. XML not used), --error-markers instructs to
           output a line containing the begin (end) string before (after) each error.

           Such marker lines facilitate searching for errors and/or extracting errors in an output
           file that contain valgrind errors mixed with the program output.

           Note that empty markers are accepted. So, only using a begin (or an end) marker is
           possible.

       --show-error-list=no|yes [default: no]
           If this option is enabled, for tools that report errors, valgrind will show the list of
           detected errors and the list of used suppressions at exit.

           Note that at verbosity 2 and above, valgrind automatically shows the list of detected
           errors and the list of used suppressions at exit, unless --show-error-list=no is
           selected.

       -s
           Specifying -s is equivalent to --show-error-list=yes.

       --sigill-diagnostics=<yes|no> [default: yes]
           Enable/disable printing of illegal instruction diagnostics. Enabled by default, but
           defaults to disabled when --quiet is given. The default can always be explicitly
           overridden by giving this option.

           When enabled, a warning message will be printed, along with some diagnostics, whenever an
           instruction is encountered that Valgrind cannot decode or translate, before the program
           is given a SIGILL signal. Often an illegal instruction indicates a bug in the program or
           missing support for the particular instruction in Valgrind. But some programs do
           deliberately try to execute an instruction that might be missing and trap the SIGILL
           signal to detect processor features. Using this flag makes it possible to avoid the
           diagnostic output that you would otherwise get in such cases.

       --keep-debuginfo=<yes|no> [default: no]
           When enabled, keep ("archive") symbols and all other debuginfo for unloaded code. This
           allows saved stack traces to include file/line info for code that has been dlclose'd (or
           similar). Be careful with this, since it can lead to unbounded memory use for programs
           which repeatedly load and unload shared objects.

           Some tools and some functionalities have only limited support for archived debug info.
           Memcheck fully supports it. Generally, tools that report errors can use archived debug
           info to show the error stack traces. The known limitations are: Helgrind's past access
           stack trace of a race condition is does not use archived debug info. Massif (and more
           generally the xtree Massif output format) does not make use of archived debug info. Only
           Memcheck has been (somewhat) tested with --keep-debuginfo=yes, so other tools may have
           unknown limitations.

       --show-below-main=<yes|no> [default: no]
           By default, stack traces for errors do not show any functions that appear beneath main
           because most of the time it's uninteresting C library stuff and/or gobbledygook.
           Alternatively, if main is not present in the stack trace, stack traces will not show any
           functions below main-like functions such as glibc's __libc_start_main. Furthermore, if
           main-like functions are present in the trace, they are normalised as (below main), in
           order to make the output more deterministic.

           If this option is enabled, all stack trace entries will be shown and main-like functions
           will not be normalised.

       --fullpath-after=<string> [default: don't show source paths]
           By default Valgrind only shows the filenames in stack traces, but not full paths to
           source files. When using Valgrind in large projects where the sources reside in multiple
           different directories, this can be inconvenient.  --fullpath-after provides a flexible
           solution to this problem. When this option is present, the path to each source file is
           shown, with the following all-important caveat: if string is found in the path, then the
           path up to and including string is omitted, else the path is shown unmodified. Note that
           string is not required to be a prefix of the path.

           For example, consider a file named /home/janedoe/blah/src/foo/bar/xyzzy.c. Specifying
           --fullpath-after=/home/janedoe/blah/src/ will cause Valgrind to show the name as
           foo/bar/xyzzy.c.

           Because the string is not required to be a prefix, --fullpath-after=src/ will produce the
           same output. This is useful when the path contains arbitrary machine-generated
           characters. For example, the path /my/build/dir/C32A1B47/blah/src/foo/xyzzy can be pruned
           to foo/xyzzy using --fullpath-after=/blah/src/.

           If you simply want to see the full path, just specify an empty string: --fullpath-after=.
           This isn't a special case, merely a logical consequence of the above rules.

           Finally, you can use --fullpath-after multiple times. Any appearance of it causes
           Valgrind to switch to producing full paths and applying the above filtering rule. Each
           produced path is compared against all the --fullpath-after-specified strings, in the
           order specified. The first string to match causes the path to be truncated as described
           above. If none match, the full path is shown. This facilitates chopping off prefixes when
           the sources are drawn from a number of unrelated directories.

       --extra-debuginfo-path=<path> [default: undefined and unused]
           By default Valgrind searches in several well-known paths for debug objects, such as
           /usr/lib/debug/.

           However, there may be scenarios where you may wish to put debug objects at an arbitrary
           location, such as external storage when running Valgrind on a mobile device with limited
           local storage. Another example might be a situation where you do not have permission to
           install debug object packages on the system where you are running Valgrind.

           In these scenarios, you may provide an absolute path as an extra, final place for
           Valgrind to search for debug objects by specifying
           --extra-debuginfo-path=/path/to/debug/objects. The given path will be prepended to the
           absolute path name of the searched-for object. For example, if Valgrind is looking for
           the debuginfo for /w/x/y/zz.so and --extra-debuginfo-path=/a/b/c is specified, it will
           look for a debug object at /a/b/c/w/x/y/zz.so.

           This flag should only be specified once. If it is specified multiple times, only the last
           instance is honoured.

       --debuginfo-server=ipaddr:port [default: undefined and unused]
           This is a new, experimental, feature introduced in version 3.9.0.

           In some scenarios it may be convenient to read debuginfo from objects stored on a
           different machine. With this flag, Valgrind will query a debuginfo server running on
           ipaddr and listening on port port, if it cannot find the debuginfo object in the local
           filesystem.

           The debuginfo server must accept TCP connections on port port. The debuginfo server is
           contained in the source file auxprogs/valgrind-di-server.c. It will only serve from the
           directory it is started in.  port defaults to 1500 in both client and server if not
           specified.

           If Valgrind looks for the debuginfo for /w/x/y/zz.so by using the debuginfo server, it
           will strip the pathname components and merely request zz.so on the server. That in turn
           will look only in its current working directory for a matching debuginfo object.

           The debuginfo data is transmitted in small fragments (8 KB) as requested by Valgrind.
           Each block is compressed using LZO to reduce transmission time. The implementation has
           been tuned for best performance over a single-stage 802.11g (WiFi) network link.

           Note that checks for matching primary vs debug objects, using GNU debuglink CRC scheme,
           are performed even when using the debuginfo server. To disable such checking, you need to
           also specify --allow-mismatched-debuginfo=yes.

           By default the Valgrind build system will build valgrind-di-server for the target
           platform, which is almost certainly not what you want. So far we have been unable to find
           out how to get automake/autoconf to build it for the build platform. If you want to use
           it, you will have to recompile it by hand using the command shown at the top of
           auxprogs/valgrind-di-server.c.

       --allow-mismatched-debuginfo=no|yes [no]
           When reading debuginfo from separate debuginfo objects, Valgrind will by default check
           that the main and debuginfo objects match, using the GNU debuglink mechanism. This
           guarantees that it does not read debuginfo from out of date debuginfo objects, and also
           ensures that Valgrind can't crash as a result of mismatches.

           This check can be overridden using --allow-mismatched-debuginfo=yes. This may be useful
           when the debuginfo and main objects have not been split in the proper way. Be careful
           when using this, though: it disables all consistency checking, and Valgrind has been
           observed to crash when the main and debuginfo objects don't match.

       --suppressions=<filename> [default: $PREFIX/lib/valgrind/default.supp]
           Specifies an extra file from which to read descriptions of errors to suppress. You may
           use up to 100 extra suppression files.

       --gen-suppressions=<yes|no|all> [default: no]
           When set to yes, Valgrind will pause after every error shown and print the line:

                   ---- Print suppression ? --- [Return/N/n/Y/y/C/c] ----

           Pressing Ret, or N Ret or n Ret, causes Valgrind continue execution without printing a
           suppression for this error.

           Pressing Y Ret or y Ret causes Valgrind to write a suppression for this error. You can
           then cut and paste it into a suppression file if you don't want to hear about the error
           in the future.

           When set to all, Valgrind will print a suppression for every reported error, without
           querying the user.

           This option is particularly useful with C++ programs, as it prints out the suppressions
           with mangled names, as required.

           Note that the suppressions printed are as specific as possible. You may want to common up
           similar ones, by adding wildcards to function names, and by using frame-level wildcards.
           The wildcarding facilities are powerful yet flexible, and with a bit of careful editing,
           you may be able to suppress a whole family of related errors with only a few
           suppressions.

           Sometimes two different errors are suppressed by the same suppression, in which case
           Valgrind will output the suppression more than once, but you only need to have one copy
           in your suppression file (but having more than one won't cause problems). Also, the
           suppression name is given as <insert a suppression name here>; the name doesn't really
           matter, it's only used with the -v option which prints out all used suppression records.

       --input-fd=<number> [default: 0, stdin]
           When using --gen-suppressions=yes, Valgrind will stop so as to read keyboard input from
           you when each error occurs. By default it reads from the standard input (stdin), which is
           problematic for programs which close stdin. This option allows you to specify an
           alternative file descriptor from which to read input.

       --dsymutil=no|yes [yes]
           This option is only relevant when running Valgrind on Mac OS X.

           Mac OS X uses a deferred debug information (debuginfo) linking scheme. When object files
           containing debuginfo are linked into a .dylib or an executable, the debuginfo is not
           copied into the final file. Instead, the debuginfo must be linked manually by running
           dsymutil, a system-provided utility, on the executable or .dylib. The resulting combined
           debuginfo is placed in a directory alongside the executable or .dylib, but with the
           extension .dSYM.

           With --dsymutil=no, Valgrind will detect cases where the .dSYM directory is either
           missing, or is present but does not appear to match the associated executable or .dylib,
           most likely because it is out of date. In these cases, Valgrind will print a warning
           message but take no further action.

           With --dsymutil=yes, Valgrind will, in such cases, automatically run dsymutil as
           necessary to bring the debuginfo up to date. For all practical purposes, if you always
           use --dsymutil=yes, then there is never any need to run dsymutil manually or as part of
           your applications's build system, since Valgrind will run it as necessary.

           Valgrind will not attempt to run dsymutil on any executable or library in /usr/, /bin/,
           /sbin/, /opt/, /sw/, /System/, /Library/ or /Applications/ since dsymutil will always
           fail in such situations. It fails both because the debuginfo for such pre-installed
           system components is not available anywhere, and also because it would require write
           privileges in those directories.

           Be careful when using --dsymutil=yes, since it will cause pre-existing .dSYM directories
           to be silently deleted and re-created. Also note that dsymutil is quite slow, sometimes
           excessively so.

       --max-stackframe=<number> [default: 2000000]
           The maximum size of a stack frame. If the stack pointer moves by more than this amount
           then Valgrind will assume that the program is switching to a different stack.

           You may need to use this option if your program has large stack-allocated arrays.
           Valgrind keeps track of your program's stack pointer. If it changes by more than the
           threshold amount, Valgrind assumes your program is switching to a different stack, and
           Memcheck behaves differently than it would for a stack pointer change smaller than the
           threshold. Usually this heuristic works well. However, if your program allocates large
           structures on the stack, this heuristic will be fooled, and Memcheck will subsequently
           report large numbers of invalid stack accesses. This option allows you to change the
           threshold to a different value.

           You should only consider use of this option if Valgrind's debug output directs you to do
           so. In that case it will tell you the new threshold you should specify.

           In general, allocating large structures on the stack is a bad idea, because you can
           easily run out of stack space, especially on systems with limited memory or which expect
           to support large numbers of threads each with a small stack, and also because the error
           checking performed by Memcheck is more effective for heap-allocated data than for
           stack-allocated data. If you have to use this option, you may wish to consider rewriting
           your code to allocate on the heap rather than on the stack.

       --main-stacksize=<number> [default: use current 'ulimit' value]
           Specifies the size of the main thread's stack.

           To simplify its memory management, Valgrind reserves all required space for the main
           thread's stack at startup. That means it needs to know the required stack size at
           startup.

           By default, Valgrind uses the current "ulimit" value for the stack size, or 16 MB,
           whichever is lower. In many cases this gives a stack size in the range 8 to 16 MB, which
           almost never overflows for most applications.

           If you need a larger total stack size, use --main-stacksize to specify it. Only set it as
           high as you need, since reserving far more space than you need (that is, hundreds of
           megabytes more than you need) constrains Valgrind's memory allocators and may reduce the
           total amount of memory that Valgrind can use. This is only really of significance on
           32-bit machines.

           On Linux, you may request a stack of size up to 2GB. Valgrind will stop with a diagnostic
           message if the stack cannot be allocated.

           --main-stacksize only affects the stack size for the program's initial thread. It has no
           bearing on the size of thread stacks, as Valgrind does not allocate those.

           You may need to use both --main-stacksize and --max-stackframe together. It is important
           to understand that --main-stacksize sets the maximum total stack size, whilst
           --max-stackframe specifies the largest size of any one stack frame. You will have to work
           out the --main-stacksize value for yourself (usually, if your applications segfaults).
           But Valgrind will tell you the needed --max-stackframe size, if necessary.

           As discussed further in the description of --max-stackframe, a requirement for a large
           stack is a sign of potential portability problems. You are best advised to place all
           large data in heap-allocated memory.

       --max-threads=<number> [default: 500]
           By default, Valgrind can handle to up to 500 threads. Occasionally, that number is too
           small. Use this option to provide a different limit. E.g.  --max-threads=3000.

MALLOC()-RELATED OPTIONS
       For tools that use their own version of malloc (e.g. Memcheck, Massif, Helgrind, DRD), the
       following options apply.

       --alignment=<number> [default: 8 or 16, depending on the platform]
           By default Valgrind's malloc, realloc, etc, return a block whose starting address is
           8-byte aligned or 16-byte aligned (the value depends on the platform and matches the
           platform default). This option allows you to specify a different alignment. The supplied
           value must be greater than or equal to the default, less than or equal to 4096, and must
           be a power of two.

       --redzone-size=<number> [default: depends on the tool]
           Valgrind's malloc, realloc, etc, add padding blocks before and after each heap block
           allocated by the program being run. Such padding blocks are called redzones. The default
           value for the redzone size depends on the tool. For example, Memcheck adds and protects a
           minimum of 16 bytes before and after each block allocated by the client. This allows it
           to detect block underruns or overruns of up to 16 bytes.

           Increasing the redzone size makes it possible to detect overruns of larger distances, but
           increases the amount of memory used by Valgrind. Decreasing the redzone size will reduce
           the memory needed by Valgrind but also reduces the chances of detecting over/underruns,
           so is not recommended.

       --xtree-memory=none|allocs|full [none]
           Tools replacing Valgrind's malloc, realloc, etc, can optionally produce an execution tree
           detailing which piece of code is responsible for heap memory usage. See ???  for a
           detailed explanation about execution trees.

           When set to none, no memory execution tree is produced.

           When set to allocs, the memory execution tree gives the current number of allocated bytes
           and the current number of allocated blocks.

           When set to full, the memory execution tree gives 6 different measurements : the current
           number of allocated bytes and blocks (same values as for allocs), the total number of
           allocated bytes and blocks, the total number of freed bytes and blocks.

           Note that the overhead in cpu and memory to produce an xtree depends on the tool. The
           overhead in cpu is small for the value allocs, as the information needed to produce this
           report is maintained in any case by the tool. For massif and helgrind, specifying full
           implies to capture a stack trace for each free operation, while normally these tools only
           capture an allocation stack trace. For Memcheck, the cpu overhead for the value full is
           small, as this can only be used in combination with --keep-stacktraces=alloc-and-free or
           --keep-stacktraces=alloc-then-free, which already records a stack trace for each free
           operation. The memory overhead varies between 5 and 10 words per unique stacktrace in the
           xtree, plus the memory needed to record the stack trace for the free operations, if
           needed specifically for the xtree.

       --xtree-memory-file=<filename> [default: xtmemory.kcg.%p]
           Specifies that Valgrind should produce the xtree memory report in the specified file. Any
           %p or %q sequences appearing in the filename are expanded in exactly the same way as they
           are for --log-file. See the description of --log-file for details.

           If the filename contains the extension .ms, then the produced file format will be a
           massif output file format. If the filename contains the extension .kcg or no extension is
           provided or recognised, then the produced file format will be a callgrind output format.

           See ???  for a detailed explanation about execution trees formats.

UNCOMMON OPTIONS
       These options apply to all tools, as they affect certain obscure workings of the Valgrind
       core. Most people won't need to use them.

       --smc-check=<none|stack|all|all-non-file> [default: all-non-file for x86/amd64/s390x, stack
       for other archs]
           This option controls Valgrind's detection of self-modifying code. If no checking is done,
           when a program executes some code, then overwrites it with new code, and executes the new
           code, Valgrind will continue to execute the translations it made for the old code. This
           will likely lead to incorrect behaviour and/or crashes.

           For "modern" architectures -- anything that's not x86, amd64 or s390x -- the default is
           stack. This is because a correct program must take explicit action to reestablish D-I
           cache coherence following code modification. Valgrind observes and honours such actions,
           with the result that self-modifying code is transparently handled with zero extra cost.

           For x86, amd64 and s390x, the program is not required to notify the hardware of required
           D-I coherence syncing. Hence the default is all-non-file, which covers the normal case of
           generating code into an anonymous (non-file-backed) mmap'd area.

           The meanings of the four available settings are as follows. No detection (none), detect
           self-modifying code on the stack (which is used by GCC to implement nested functions)
           (stack), detect self-modifying code everywhere (all), and detect self-modifying code
           everywhere except in file-backed mappings (all-non-file).

           Running with all will slow Valgrind down noticeably. Running with none will rarely speed
           things up, since very little code gets dynamically generated in most programs. The
           VALGRIND_DISCARD_TRANSLATIONS client request is an alternative to --smc-check=all and
           --smc-check=all-non-file that requires more programmer effort but allows Valgrind to run
           your program faster, by telling it precisely when translations need to be re-made.

           --smc-check=all-non-file provides a cheaper but more limited version of --smc-check=all.
           It adds checks to any translations that do not originate from file-backed memory
           mappings. Typical applications that generate code, for example JITs in web browsers,
           generate code into anonymous mmaped areas, whereas the "fixed" code of the browser always
           lives in file-backed mappings.  --smc-check=all-non-file takes advantage of this
           observation, limiting the overhead of checking to code which is likely to be JIT
           generated.

       --read-inline-info=<yes|no> [default: see below]
           When enabled, Valgrind will read information about inlined function calls from DWARF3
           debug info. This slows Valgrind startup and makes it use more memory (typically for each
           inlined piece of code, 6 words and space for the function name), but it results in more
           descriptive stacktraces. Currently, this functionality is enabled by default only for
           Linux, Android and Solaris targets and only for the tools Memcheck, Massif, Helgrind and
           DRD. Here is an example of some stacktraces with --read-inline-info=no:

               ==15380== Conditional jump or move depends on uninitialised value(s)
               ==15380==    at 0x80484EA: main (inlinfo.c:6)
               ==15380==
               ==15380== Conditional jump or move depends on uninitialised value(s)
               ==15380==    at 0x8048550: fun_noninline (inlinfo.c:6)
               ==15380==    by 0x804850E: main (inlinfo.c:34)
               ==15380==
               ==15380== Conditional jump or move depends on uninitialised value(s)
               ==15380==    at 0x8048520: main (inlinfo.c:6)

           And here are the same errors with --read-inline-info=yes:

               ==15377== Conditional jump or move depends on uninitialised value(s)
               ==15377==    at 0x80484EA: fun_d (inlinfo.c:6)
               ==15377==    by 0x80484EA: fun_c (inlinfo.c:14)
               ==15377==    by 0x80484EA: fun_b (inlinfo.c:20)
               ==15377==    by 0x80484EA: fun_a (inlinfo.c:26)
               ==15377==    by 0x80484EA: main (inlinfo.c:33)
               ==15377==
               ==15377== Conditional jump or move depends on uninitialised value(s)
               ==15377==    at 0x8048550: fun_d (inlinfo.c:6)
               ==15377==    by 0x8048550: fun_noninline (inlinfo.c:41)
               ==15377==    by 0x804850E: main (inlinfo.c:34)
               ==15377==
               ==15377== Conditional jump or move depends on uninitialised value(s)
               ==15377==    at 0x8048520: fun_d (inlinfo.c:6)
               ==15377==    by 0x8048520: main (inlinfo.c:35)

       --read-var-info=<yes|no> [default: no]
           When enabled, Valgrind will read information about variable types and locations from
           DWARF3 debug info. This slows Valgrind startup significantly and makes it use
           significantly more memory, but for the tools that can take advantage of it (Memcheck,
           Helgrind, DRD) it can result in more precise error messages. For example, here are some
           standard errors issued by Memcheck:

               ==15363== Uninitialised byte(s) found during client check request
               ==15363==    at 0x80484A9: croak (varinfo1.c:28)
               ==15363==    by 0x8048544: main (varinfo1.c:55)
               ==15363==  Address 0x80497f7 is 7 bytes inside data symbol "global_i2"
               ==15363==
               ==15363== Uninitialised byte(s) found during client check request
               ==15363==    at 0x80484A9: croak (varinfo1.c:28)
               ==15363==    by 0x8048550: main (varinfo1.c:56)
               ==15363==  Address 0xbea0d0cc is on thread 1's stack
               ==15363==  in frame #1, created by main (varinfo1.c:45)

           And here are the same errors with --read-var-info=yes:

               ==15370== Uninitialised byte(s) found during client check request
               ==15370==    at 0x80484A9: croak (varinfo1.c:28)
               ==15370==    by 0x8048544: main (varinfo1.c:55)
               ==15370==  Location 0x80497f7 is 0 bytes inside global_i2[7],
               ==15370==  a global variable declared at varinfo1.c:41
               ==15370==
               ==15370== Uninitialised byte(s) found during client check request
               ==15370==    at 0x80484A9: croak (varinfo1.c:28)
               ==15370==    by 0x8048550: main (varinfo1.c:56)
               ==15370==  Location 0xbeb4a0cc is 0 bytes inside local var "local"
               ==15370==  declared at varinfo1.c:46, in frame #1 of thread 1

       --vgdb-poll=<number> [default: 5000]
           As part of its main loop, the Valgrind scheduler will poll to check if some activity
           (such as an external command or some input from a gdb) has to be handled by gdbserver.
           This activity poll will be done after having run the given number of basic blocks (or
           slightly more than the given number of basic blocks). This poll is quite cheap so the
           default value is set relatively low. You might further decrease this value if vgdb cannot
           use ptrace system call to interrupt Valgrind if all threads are (most of the time)
           blocked in a system call.

       --vgdb-shadow-registers=no|yes [default: no]
           When activated, gdbserver will expose the Valgrind shadow registers to GDB. With this,
           the value of the Valgrind shadow registers can be examined or changed using GDB. Exposing
           shadow registers only works with GDB version 7.1 or later.

       --vgdb-prefix=<prefix> [default: /tmp/vgdb-pipe]
           To communicate with gdb/vgdb, the Valgrind gdbserver creates 3 files (2 named FIFOs and a
           mmap shared memory file). The prefix option controls the directory and prefix for the
           creation of these files.

       --run-libc-freeres=<yes|no> [default: yes]
           This option is only relevant when running Valgrind on Linux.

           The GNU C library (libc.so), which is used by all programs, may allocate memory for its
           own uses. Usually it doesn't bother to free that memory when the program ends—there would
           be no point, since the Linux kernel reclaims all process resources when a process exits
           anyway, so it would just slow things down.

           The glibc authors realised that this behaviour causes leak checkers, such as Valgrind, to
           falsely report leaks in glibc, when a leak check is done at exit. In order to avoid this,
           they provided a routine called __libc_freeres specifically to make glibc release all
           memory it has allocated. Memcheck therefore tries to run __libc_freeres at exit.

           Unfortunately, in some very old versions of glibc, __libc_freeres is sufficiently buggy
           to cause segmentation faults. This was particularly noticeable on Red Hat 7.1. So this
           option is provided in order to inhibit the run of __libc_freeres. If your program seems
           to run fine on Valgrind, but segfaults at exit, you may find that --run-libc-freeres=no
           fixes that, although at the cost of possibly falsely reporting space leaks in libc.so.

       --run-cxx-freeres=<yes|no> [default: yes]
           This option is only relevant when running Valgrind on Linux or Solaris C++ programs.

           The GNU Standard C++ library (libstdc++.so), which is used by all C++ programs compiled
           with g++, may allocate memory for its own uses. Usually it doesn't bother to free that
           memory when the program ends—there would be no point, since the kernel reclaims all
           process resources when a process exits anyway, so it would just slow things down.

           The gcc authors realised that this behaviour causes leak checkers, such as Valgrind, to
           falsely report leaks in libstdc++, when a leak check is done at exit. In order to avoid
           this, they provided a routine called __gnu_cxx::__freeres specifically to make libstdc++
           release all memory it has allocated. Memcheck therefore tries to run __gnu_cxx::__freeres
           at exit.

           For the sake of flexibility and unforeseen problems with __gnu_cxx::__freeres, option
           --run-cxx-freeres=no exists, although at the cost of possibly falsely reporting space
           leaks in libstdc++.so.

       --sim-hints=hint1,hint2,...
           Pass miscellaneous hints to Valgrind which slightly modify the simulated behaviour in
           nonstandard or dangerous ways, possibly to help the simulation of strange features. By
           default no hints are enabled. Use with caution! Currently known hints are:

           ·   lax-ioctls: Be very lax about ioctl handling; the only assumption is that the size is
               correct. Doesn't require the full buffer to be initialised when writing. Without
               this, using some device drivers with a large number of strange ioctl commands becomes
               very tiresome.

           ·   fuse-compatible: Enable special handling for certain system calls that may block in a
               FUSE file-system. This may be necessary when running Valgrind on a multi-threaded
               program that uses one thread to manage a FUSE file-system and another thread to
               access that file-system.

           ·   enable-outer: Enable some special magic needed when the program being run is itself
               Valgrind.

           ·   no-inner-prefix: Disable printing a prefix > in front of each stdout or stderr output
               line in an inner Valgrind being run by an outer Valgrind. This is useful when running
               Valgrind regression tests in an outer/inner setup. Note that the prefix > will always
               be printed in front of the inner debug logging lines.

           ·   no-nptl-pthread-stackcache: This hint is only relevant when running Valgrind on
               Linux; it is ignored on Solaris and Mac OS X.

               The GNU glibc pthread library (libpthread.so), which is used by pthread programs,
               maintains a cache of pthread stacks. When a pthread terminates, the memory used for
               the pthread stack and some thread local storage related data structure are not always
               directly released. This memory is kept in a cache (up to a certain size), and is
               re-used if a new thread is started.

               This cache causes the helgrind tool to report some false positive race condition
               errors on this cached memory, as helgrind does not understand the internal glibc
               cache synchronisation primitives. So, when using helgrind, disabling the cache helps
               to avoid false positive race conditions, in particular when using thread local
               storage variables (e.g. variables using the __thread qualifier).

               When using the memcheck tool, disabling the cache ensures the memory used by glibc to
               handle __thread variables is directly released when a thread terminates.

               Note: Valgrind disables the cache using some internal knowledge of the glibc stack
               cache implementation and by examining the debug information of the pthread library.
               This technique is thus somewhat fragile and might not work for all glibc versions.
               This has been successfully tested with various glibc versions (e.g. 2.11, 2.16, 2.18)
               on various platforms.

           ·   lax-doors: (Solaris only) Be very lax about door syscall handling over unrecognised
               door file descriptors. Does not require that full buffer is initialised when writing.
               Without this, programs using libdoor(3LIB) functionality with completely proprietary
               semantics may report large number of false positives.

           ·   fallback-llsc: (MIPS and ARM64 only): Enables an alternative implementation of
               Load-Linked (LL) and Store-Conditional (SC) instructions. The standard implementation
               gives more correct behaviour, but can cause indefinite looping on certain processor
               implementations that are intolerant of extra memory references between LL and SC. So
               far this is known only to happen on Cavium 3 cores. You should not need to use this
               flag, since the relevant cores are detected at startup and the alternative
               implementation is automatically enabled if necessary. There is no equivalent
               anti-flag: you cannot force-disable the alternative implementation, if it is
               automatically enabled. The underlying problem exists because the "standard"
               implementation of LL and SC is done by copying through LL and SC instructions into
               the instrumented code. However, tools may insert extra instrumentation memory
               references in between the LL and SC instructions. These memory references are not
               present in the original uninstrumented code, and their presence in the instrumented
               code can cause the SC instructions to persistently fail, leading to indefinite
               looping in LL-SC blocks. The alternative implementation gives correct behaviour of LL
               and SC instructions between threads in a process, up to and including the ABA
               scenario. It also gives correct behaviour between a Valgrinded thread and a
               non-Valgrinded thread running in a different process, that communicate via shared
               memory, but only up to and including correct CAS behaviour -- in this case the ABA
               scenario may not be correctly handled.

       --fair-sched=<no|yes|try> [default: no]
           The --fair-sched option controls the locking mechanism used by Valgrind to serialise
           thread execution. The locking mechanism controls the way the threads are scheduled, and
           different settings give different trade-offs between fairness and performance. For more
           details about the Valgrind thread serialisation scheme and its impact on performance and
           thread scheduling, see Scheduling and Multi-Thread Performance.

           ·   The value --fair-sched=yes activates a fair scheduler. In short, if multiple threads
               are ready to run, the threads will be scheduled in a round robin fashion. This
               mechanism is not available on all platforms or Linux versions. If not available,
               using --fair-sched=yes will cause Valgrind to terminate with an error.

               You may find this setting improves overall responsiveness if you are running an
               interactive multithreaded program, for example a web browser, on Valgrind.

           ·   The value --fair-sched=try activates fair scheduling if available on the platform.
               Otherwise, it will automatically fall back to --fair-sched=no.

           ·   The value --fair-sched=no activates a scheduler which does not guarantee fairness
               between threads ready to run, but which in general gives the highest performance.

       --kernel-variant=variant1,variant2,...
           Handle system calls and ioctls arising from minor variants of the default kernel for this
           platform. This is useful for running on hacked kernels or with kernel modules which
           support nonstandard ioctls, for example. Use with caution. If you don't understand what
           this option does then you almost certainly don't need it. Currently known variants are:

           ·   bproc: support the sys_broc system call on x86. This is for running on BProc, which
               is a minor variant of standard Linux which is sometimes used for building clusters.

           ·   android-no-hw-tls: some versions of the Android emulator for ARM do not provide a
               hardware TLS (thread-local state) register, and Valgrind crashes at startup. Use this
               variant to select software support for TLS.

           ·   android-gpu-sgx5xx: use this to support handling of proprietary ioctls for the
               PowerVR SGX 5XX series of GPUs on Android devices. Failure to select this does not
               cause stability problems, but may cause Memcheck to report false errors after the
               program performs GPU-specific ioctls.

           ·   android-gpu-adreno3xx: similarly, use this to support handling of proprietary ioctls
               for the Qualcomm Adreno 3XX series of GPUs on Android devices.

       --merge-recursive-frames=<number> [default: 0]
           Some recursive algorithms, for example balanced binary tree implementations, create many
           different stack traces, each containing cycles of calls. A cycle is defined as two
           identical program counter values separated by zero or more other program counter values.
           Valgrind may then use a lot of memory to store all these stack traces. This is a poor use
           of memory considering that such stack traces contain repeated uninteresting recursive
           calls instead of more interesting information such as the function that has initiated the
           recursive call.

           The option --merge-recursive-frames=<number> instructs Valgrind to detect and merge
           recursive call cycles having a size of up to <number> frames. When such a cycle is
           detected, Valgrind records the cycle in the stack trace as a unique program counter.

           The value 0 (the default) causes no recursive call merging. A value of 1 will cause stack
           traces of simple recursive algorithms (for example, a factorial implementation) to be
           collapsed. A value of 2 will usually be needed to collapse stack traces produced by
           recursive algorithms such as binary trees, quick sort, etc. Higher values might be needed
           for more complex recursive algorithms.

           Note: recursive calls are detected by analysis of program counter values. They are not
           detected by looking at function names.

       --num-transtab-sectors=<number> [default: 6 for Android platforms, 16 for all others]
           Valgrind translates and instruments your program's machine code in small fragments (basic
           blocks). The translations are stored in a translation cache that is divided into a number
           of sections (sectors). If the cache is full, the sector containing the oldest
           translations is emptied and reused. If these old translations are needed again, Valgrind
           must re-translate and re-instrument the corresponding machine code, which is expensive.
           If the "executed instructions" working set of a program is big, increasing the number of
           sectors may improve performance by reducing the number of re-translations needed. Sectors
           are allocated on demand. Once allocated, a sector can never be freed, and occupies
           considerable space, depending on the tool and the value of --avg-transtab-entry-size
           (about 40 MB per sector for Memcheck). Use the option --stats=yes to obtain precise
           information about the memory used by a sector and the allocation and recycling of
           sectors.

       --avg-transtab-entry-size=<number> [default: 0, meaning use tool provided default]
           Average size of translated basic block. This average size is used to dimension the size
           of a sector. Each tool provides a default value to be used. If this default value is too
           small, the translation sectors will become full too quickly. If this default value is too
           big, a significant part of the translation sector memory will be unused. Note that the
           average size of a basic block translation depends on the tool, and might depend on tool
           options. For example, the memcheck option --track-origins=yes increases the size of the
           basic block translations. Use --avg-transtab-entry-size to tune the size of the sectors,
           either to gain memory or to avoid too many retranslations.

       --aspace-minaddr=<address> [default: depends on the platform]
           To avoid potential conflicts with some system libraries, Valgrind does not use the
           address space below --aspace-minaddr value, keeping it reserved in case a library
           specifically requests memory in this region. So, some "pessimistic" value is guessed by
           Valgrind depending on the platform. On linux, by default, Valgrind avoids using the first
           64MB even if typically there is no conflict in this complete zone. You can use the option
           --aspace-minaddr to have your memory hungry application benefitting from more of this
           lower memory. On the other hand, if you encounter a conflict, increasing aspace-minaddr
           value might solve it. Conflicts will typically manifest themselves with mmap failures in
           the low range of the address space. The provided address must be page aligned and must be
           equal or bigger to 0x1000 (4KB). To find the default value on your platform, do something
           such as valgrind -d -d date 2>&1 | grep -i minaddr. Values lower than 0x10000 (64KB) are
           known to create problems on some distributions.

       --valgrind-stacksize=<number> [default: 1MB]
           For each thread, Valgrind needs its own 'private' stack. The default size for these
           stacks is largely dimensioned, and so should be sufficient in most cases. In case the
           size is too small, Valgrind will segfault. Before segfaulting, a warning might be
           produced by Valgrind when approaching the limit.

           Use the option --valgrind-stacksize if such an (unlikely) warning is produced, or
           Valgrind dies due to a segmentation violation. Such segmentation violations have been
           seen when demangling huge C++ symbols.

           If your application uses many threads and needs a lot of memory, you can gain some memory
           by reducing the size of these Valgrind stacks using the option --valgrind-stacksize.

       --show-emwarns=<yes|no> [default: no]
           When enabled, Valgrind will emit warnings about its CPU emulation in certain cases. These
           are usually not interesting.

       --require-text-symbol=:sonamepatt:fnnamepatt
           When a shared object whose soname matches sonamepatt is loaded into the process, examine
           all the text symbols it exports. If none of those match fnnamepatt, print an error
           message and abandon the run. This makes it possible to ensure that the run does not
           continue unless a given shared object contains a particular function name.

           Both sonamepatt and fnnamepatt can be written using the usual ?  and * wildcards. For
           example: ":*libc.so*:foo?bar". You may use characters other than a colon to separate the
           two patterns. It is only important that the first character and the separator character
           are the same. For example, the above example could also be written "Q*libc.so*Qfoo?bar".
           Multiple
            --require-text-symbol flags are allowed, in which case shared objects that are loaded
           into the process will be checked against all of them.

           The purpose of this is to support reliable usage of marked-up libraries. For example,
           suppose we have a version of GCC's libgomp.so which has been marked up with annotations
           to support Helgrind. It is only too easy and confusing to load the wrong, un-annotated
           libgomp.so into the application. So the idea is: add a text symbol in the marked-up
           library, for example annotated_for_helgrind_3_6, and then give the flag
           --require-text-symbol=:*libgomp*so*:annotated_for_helgrind_3_6 so that when libgomp.so is
           loaded, Valgrind scans its symbol table, and if the symbol isn't present the run is
           aborted, rather than continuing silently with the un-marked-up library. Note that you
           should put the entire flag in quotes to stop shells expanding up the * and ?  wildcards.

       --soname-synonyms=syn1=pattern1,syn2=pattern2,...
           When a shared library is loaded, Valgrind checks for functions in the library that must
           be replaced or wrapped. For example, Memcheck replaces some string and memory functions
           (strchr, strlen, strcpy, memchr, memcpy, memmove, etc.) with its own versions. Such
           replacements are normally done only in shared libraries whose soname matches a predefined
           soname pattern (e.g.  libc.so* on linux). By default, no replacement is done for a
           statically linked binary or for alternative libraries, except for the allocation
           functions (malloc, free, calloc, memalign, realloc, operator new, operator delete, etc.)
           Such allocation functions are intercepted by default in any shared library or in the
           executable if they are exported as global symbols. This means that if a replacement
           allocation library such as tcmalloc is found, its functions are also intercepted by
           default. In some cases, the replacements allow --soname-synonyms to specify one
           additional synonym pattern, giving flexibility in the replacement. Or to prevent
           interception of all public allocation symbols.

           Currently, this flexibility is only allowed for the malloc related functions, using the
           synonym somalloc. This synonym is usable for all tools doing standard replacement of
           malloc related functions (e.g. memcheck, helgrind, drd, massif, dhat, exp-sgcheck).

           ·   Alternate malloc library: to replace the malloc related functions in a specific
               alternate library with soname mymalloclib.so (and not in any others), give the option
               --soname-synonyms=somalloc=mymalloclib.so. A pattern can be used to match multiple
               libraries sonames. For example, --soname-synonyms=somalloc=*tcmalloc* will match the
               soname of all variants of the tcmalloc library (native, debug, profiled, ... tcmalloc
               variants).

               Note: the soname of a elf shared library can be retrieved using the readelf utility.

           ·   Replacements in a statically linked library are done by using the NONE pattern. For
               example, if you link with libtcmalloc.a, and only want to intercept the malloc
               related functions in the executable (and standard libraries) themselves, but not any
               other shared libraries, you can give the option --soname-synonyms=somalloc=NONE. Note
               that a NONE pattern will match the main executable and any shared library having no
               soname.

           ·   To run a "default" Firefox build for Linux, in which JEMalloc is linked in to the
               main executable, use --soname-synonyms=somalloc=NONE.

           ·   To only intercept allocation symbols in the default system libraries, but not in any
               other shared library or the executable defining public malloc or operator new related
               functions use a non-existing library name like
               --soname-synonyms=somalloc=nouserintercepts (where nouserintercepts can be any
               non-existing library name).

           ·   Shared library of the dynamic (runtime) linker is excluded from searching for global
               public symbols, such as those for the malloc related functions (identified by
               somalloc synonym).

       --progress-interval=<number> [default: 0, meaning 'disabled']
           This is an enhancement to Valgrind's debugging output. It is unlikely to be of interest
           to end users.

           When number is set to a non-zero value, Valgrind will print a one-line progress summary
           every number seconds. Valid settings for number are between 0 and 3600 inclusive. Here's
           some example output with number set to 10:

               PROGRESS: U 110s, W 113s, 97.3% CPU, EvC 414.79M, TIn 616.7k, TOut 0.5k, #thr 67
               PROGRESS: U 120s, W 124s, 96.8% CPU, EvC 505.27M, TIn 636.6k, TOut 3.0k, #thr 64
               PROGRESS: U 130s, W 134s, 97.0% CPU, EvC 574.90M, TIn 657.5k, TOut 3.0k, #thr 63

           Each line shows:
			·   U: total user time.RE
			·   W: total wallclock time.RE
            ·   CPU: overall average cpu use.RE
			·   EvC: number of event checks.  An event check is a backwards branch in the simulated program, 
				so this is a measure of forward progress of the program.RE
            ·   TIn: number of code blocks instrumented by the JIT.RE
			·   TOut: number of instrumented code blocks that have been thrown away.RE
			·   #thr: number of threads in the program.RE From the progress of these, it is possible to observe:
			·   when the program is compute bound (TIn rises slowly, EvC rises rapidly).RE
			·   when the program is in a spinloop (TIn/TOut fixed, EvC rises rapidly).RE
			·   when the program is JIT-bound (TIn rises rapidly).RE
			·   when the program is rapidly discarding code  (TOut rises rapidly).RE
			·   when the program is about to achieve some expected state (EvC arrives at some value you expect).RE
			·    when the program is idling (U rises more slowly than W).RE


DEBUGGING VALGRIND OPTIONS
       There are also some options for debugging Valgrind itself. You shouldn't need to use them in
       the normal run of things. If you wish to see the list, use the --help-debug option.

MEMCHECK OPTIONS
       --leak-check=<no|summary|yes|full> [default: summary]
           When enabled, search for memory leaks when the client program finishes. If set to
           summary, it says how many leaks occurred. If set to full or yes, each individual leak
           will be shown in detail and/or counted as an error, as specified by the options
           --show-leak-kinds and --errors-for-leak-kinds.

       --leak-resolution=<low|med|high> [default: high]
           When doing leak checking, determines how willing Memcheck is to consider different
           backtraces to be the same for the purposes of merging multiple leaks into a single leak
           report. When set to low, only the first two entries need match. When med, four entries
           have to match. When high, all entries need to match.

           For hardcore leak debugging, you probably want to use --leak-resolution=high together
           with --num-callers=40 or some such large number.

           Note that the --leak-resolution setting does not affect Memcheck's ability to find leaks.
           It only changes how the results are presented.

       --show-leak-kinds=<set> [default: definite,possible]
           Specifies the leak kinds to show in a full leak search, in one of the following ways:

           ·   a comma separated list of one or more of definite indirect possible reachable.

           ·   all to specify the complete set (all leak kinds). It is equivalent to
               --show-leak-kinds=definite,indirect,possible,reachable.

           ·   none for the empty set.

       --errors-for-leak-kinds=<set> [default: definite,possible]
           Specifies the leak kinds to count as errors in a full leak search. The <set> is specified
           similarly to --show-leak-kinds

       --leak-check-heuristics=<set> [default: all]
           Specifies the set of leak check heuristics to be used during leak searches. The
           heuristics control which interior pointers to a block cause it to be considered as
           reachable. The heuristic set is specified in one of the following ways:

           ·   a comma separated list of one or more of stdstring length64 newarray
               multipleinheritance.

           ·   all to activate the complete set of heuristics. It is equivalent to
               --leak-check-heuristics=stdstring,length64,newarray,multipleinheritance.

           ·   none for the empty set.

           Note that these heuristics are dependent on the layout of the objects produced by the C++
           compiler. They have been tested with some gcc versions (e.g. 4.4 and 4.7). They might not
           work properly with other C++ compilers.

       --show-reachable=<yes|no> , --show-possibly-lost=<yes|no>
           These options provide an alternative way to specify the leak kinds to show:

           ·   --show-reachable=no --show-possibly-lost=yes is equivalent to
               --show-leak-kinds=definite,possible.

           ·   --show-reachable=no --show-possibly-lost=no is equivalent to
               --show-leak-kinds=definite.

           ·   --show-reachable=yes is equivalent to --show-leak-kinds=all.

           Note that --show-possibly-lost=no has no effect if --show-reachable=yes is specified.

       --xtree-leak=<no|yes> [no]
           If set to yes, the results for the leak search done at exit will be output in a
           'Callgrind Format' execution tree file. Note that this automatically sets the options
           --leak-check=full and --show-leak-kinds=all, to allow xtree visualisation tools such as
           kcachegrind to select what kind to leak to visualise. The produced file will contain the
           following events:

           ·   RB : Reachable Bytes

           ·   PB : Possibly lost Bytes

           ·   IB : Indirectly lost Bytes

           ·   DB : Definitely lost Bytes (direct plus indirect)

           ·   DIB : Definitely Indirectly lost Bytes (subset of DB)

           ·   RBk : reachable Blocks

           ·   PBk : Possibly lost Blocks

           ·   IBk : Indirectly lost Blocks

           ·   DBk : Definitely lost Blocks

           The increase or decrease for all events above will also be output in the file to provide
           the delta (increase or decrease) between 2 successive leak searches. For example, iRB is
           the increase of the RB event, dPBk is the decrease of PBk event. The values for the
           increase and decrease events will be zero for the first leak search done.

           See ???  for a detailed explanation about execution trees.

       --xtree-leak-file=<filename> [default: xtleak.kcg.%p]
           Specifies that Valgrind should produce the xtree leak report in the specified file. Any
           %p, %q or %n sequences appearing in the filename are expanded in exactly the same way as
           they are for --log-file. See the description of --log-file for details.

           See ???  for a detailed explanation about execution trees formats.

       --undef-value-errors=<yes|no> [default: yes]
           Controls whether Memcheck reports uses of undefined value errors. Set this to no if you
           don't want to see undefined value errors. It also has the side effect of speeding up
           Memcheck somewhat. AddrCheck (removed in Valgrind 3.1.0) functioned like Memcheck with
           --undef-value-errors=no.

       --track-origins=<yes|no> [default: no]
           Controls whether Memcheck tracks the origin of uninitialised values. By default, it does
           not, which means that although it can tell you that an uninitialised value is being used
           in a dangerous way, it cannot tell you where the uninitialised value came from. This
           often makes it difficult to track down the root problem.

           When set to yes, Memcheck keeps track of the origins of all uninitialised values. Then,
           when an uninitialised value error is reported, Memcheck will try to show the origin of
           the value. An origin can be one of the following four places: a heap block, a stack
           allocation, a client request, or miscellaneous other sources (eg, a call to brk).

           For uninitialised values originating from a heap block, Memcheck shows where the block
           was allocated. For uninitialised values originating from a stack allocation, Memcheck can
           tell you which function allocated the value, but no more than that -- typically it shows
           you the source location of the opening brace of the function. So you should carefully
           check that all of the function's local variables are initialised properly.

           Performance overhead: origin tracking is expensive. It halves Memcheck's speed and
           increases memory use by a minimum of 100MB, and possibly more. Nevertheless it can
           drastically reduce the effort required to identify the root cause of uninitialised value
           errors, and so is often a programmer productivity win, despite running more slowly.

           Accuracy: Memcheck tracks origins quite accurately. To avoid very large space and time
           overheads, some approximations are made. It is possible, although unlikely, that Memcheck
           will report an incorrect origin, or not be able to identify any origin.

           Note that the combination --track-origins=yes and --undef-value-errors=no is nonsensical.
           Memcheck checks for and rejects this combination at startup.

       --partial-loads-ok=<yes|no> [default: yes]
           Controls how Memcheck handles 32-, 64-, 128- and 256-bit naturally aligned loads from
           addresses for which some bytes are addressable and others are not. When yes, such loads
           do not produce an address error. Instead, loaded bytes originating from illegal addresses
           are marked as uninitialised, and those corresponding to legal addresses are handled in
           the normal way.

           When no, loads from partially invalid addresses are treated the same as loads from
           completely invalid addresses: an illegal-address error is issued, and the resulting bytes
           are marked as initialised.

           Note that code that behaves in this way is in violation of the ISO C/C++ standards, and
           should be considered broken. If at all possible, such code should be fixed.

       --expensive-definedness-checks=<no|auto|yes> [default: auto]
           Controls whether Memcheck should employ more precise but also more expensive (time
           consuming) instrumentation when checking the definedness of certain values. In
           particular, this affects the instrumentation of integer adds, subtracts and equality
           comparisons.

           Selecting --expensive-definedness-checks=yes causes Memcheck to use the most accurate
           analysis possible. This minimises false error rates but can cause up to 30% performance
           degradation.

           Selecting --expensive-definedness-checks=no causes Memcheck to use the cheapest
           instrumentation possible. This maximises performance but will normally give an unusably
           high false error rate.

           The default setting, --expensive-definedness-checks=auto, is strongly recommended. This
           causes Memcheck to use the minimum of expensive instrumentation needed to achieve the
           same false error rate as --expensive-definedness-checks=yes. It also enables an
           instrumentation-time analysis pass which aims to further reduce the costs of accurate
           instrumentation. Overall, the performance loss is generally around 5% relative to
           --expensive-definedness-checks=no, although this is strongly workload dependent. Note
           that the exact instrumentation settings in this mode are architecture dependent.

       --keep-stacktraces=alloc|free|alloc-and-free|alloc-then-free|none [default: alloc-and-free]
           Controls which stack trace(s) to keep for malloc'd and/or free'd blocks.

           With alloc-then-free, a stack trace is recorded at allocation time, and is associated
           with the block. When the block is freed, a second stack trace is recorded, and this
           replaces the allocation stack trace. As a result, any "use after free" errors relating to
           this block can only show a stack trace for where the block was freed.

           With alloc-and-free, both allocation and the deallocation stack traces for the block are
           stored. Hence a "use after free" error will show both, which may make the error easier to
           diagnose. Compared to alloc-then-free, this setting slightly increases Valgrind's memory
           use as the block contains two references instead of one.

           With alloc, only the allocation stack trace is recorded (and reported). With free, only
           the deallocation stack trace is recorded (and reported). These values somewhat decrease
           Valgrind's memory and cpu usage. They can be useful depending on the error types you are
           searching for and the level of detail you need to analyse them. For example, if you are
           only interested in memory leak errors, it is sufficient to record the allocation stack
           traces.

           With none, no stack traces are recorded for malloc and free operations. If your program
           allocates a lot of blocks and/or allocates/frees from many different stack traces, this
           can significantly decrease cpu and/or memory required. Of course, few details will be
           reported for errors related to heap blocks.

           Note that once a stack trace is recorded, Valgrind keeps the stack trace in memory even
           if it is not referenced by any block. Some programs (for example, recursive algorithms)
           can generate a huge number of stack traces. If Valgrind uses too much memory in such
           circumstances, you can reduce the memory required with the options --keep-stacktraces
           and/or by using a smaller value for the option --num-callers.

           If you want to use --xtree-memory=full memory profiling (see ???  ), then you cannot
           specify --keep-stacktraces=free or --keep-stacktraces=none.

       --freelist-vol=<number> [default: 20000000]
           When the client program releases memory using free (in C) or delete (C++), that memory is
           not immediately made available for re-allocation. Instead, it is marked inaccessible and
           placed in a queue of freed blocks. The purpose is to defer as long as possible the point
           at which freed-up memory comes back into circulation. This increases the chance that
           Memcheck will be able to detect invalid accesses to blocks for some significant period of
           time after they have been freed.

           This option specifies the maximum total size, in bytes, of the blocks in the queue. The
           default value is twenty million bytes. Increasing this increases the total amount of
           memory used by Memcheck but may detect invalid uses of freed blocks which would otherwise
           go undetected.

       --freelist-big-blocks=<number> [default: 1000000]
           When making blocks from the queue of freed blocks available for re-allocation, Memcheck
           will in priority re-circulate the blocks with a size greater or equal to
           --freelist-big-blocks. This ensures that freeing big blocks (in particular freeing blocks
           bigger than --freelist-vol) does not immediately lead to a re-circulation of all (or a
           lot of) the small blocks in the free list. In other words, this option increases the
           likelihood to discover dangling pointers for the "small" blocks, even when big blocks are
           freed.

           Setting a value of 0 means that all the blocks are re-circulated in a FIFO order.

       --workaround-gcc296-bugs=<yes|no> [default: no]
           When enabled, assume that reads and writes some small distance below the stack pointer
           are due to bugs in GCC 2.96, and does not report them. The "small distance" is 256 bytes
           by default. Note that GCC 2.96 is the default compiler on some ancient Linux
           distributions (RedHat 7.X) and so you may need to use this option. Do not use it if you
           do not have to, as it can cause real errors to be overlooked. A better alternative is to
           use a more recent GCC in which this bug is fixed.

           You may also need to use this option when working with GCC 3.X or 4.X on 32-bit PowerPC
           Linux. This is because GCC generates code which occasionally accesses below the stack
           pointer, particularly for floating-point to/from integer conversions. This is in
           violation of the 32-bit PowerPC ELF specification, which makes no provision for locations
           below the stack pointer to be accessible.

           This option is deprecated as of version 3.12 and may be removed from future versions. You
           should instead use --ignore-range-below-sp to specify the exact range of offsets below
           the stack pointer that should be ignored. A suitable equivalent is
           --ignore-range-below-sp=1024-1.

       --ignore-range-below-sp=<number>-<number>
           This is a more general replacement for the deprecated --workaround-gcc296-bugs option.
           When specified, it causes Memcheck not to report errors for accesses at the specified
           offsets below the stack pointer. The two offsets must be positive decimal numbers and --
           somewhat counterintuitively -- the first one must be larger, in order to imply a
           non-wraparound address range to ignore. For example, to ignore 4 byte accesses at 8192
           bytes below the stack pointer, use --ignore-range-below-sp=8192-8189. Only one range may
           be specified.

       --show-mismatched-frees=<yes|no> [default: yes]
           When enabled, Memcheck checks that heap blocks are deallocated using a function that
           matches the allocating function. That is, it expects free to be used to deallocate blocks
           allocated by malloc, delete for blocks allocated by new, and delete[] for blocks
           allocated by new[]. If a mismatch is detected, an error is reported. This is in general
           important because in some environments, freeing with a non-matching function can cause
           crashes.

           There is however a scenario where such mismatches cannot be avoided. That is when the
           user provides implementations of new/new[] that call malloc and of delete/delete[] that
           call free, and these functions are asymmetrically inlined. For example, imagine that
           delete[] is inlined but new[] is not. The result is that Memcheck "sees" all delete[]
           calls as direct calls to free, even when the program source contains no mismatched calls.

           This causes a lot of confusing and irrelevant error reports.  --show-mismatched-frees=no
           disables these checks. It is not generally advisable to disable them, though, because you
           may miss real errors as a result.

       --ignore-ranges=0xPP-0xQQ[,0xRR-0xSS]
           Any ranges listed in this option (and multiple ranges can be specified, separated by
           commas) will be ignored by Memcheck's addressability checking.

       --malloc-fill=<hexnumber>
           Fills blocks allocated by malloc, new, etc, but not by calloc, with the specified byte.
           This can be useful when trying to shake out obscure memory corruption problems. The
           allocated area is still regarded by Memcheck as undefined -- this option only affects its
           contents. Note that --malloc-fill does not affect a block of memory when it is used as
           argument to client requests VALGRIND_MEMPOOL_ALLOC or VALGRIND_MALLOCLIKE_BLOCK.

       --free-fill=<hexnumber>
           Fills blocks freed by free, delete, etc, with the specified byte value. This can be
           useful when trying to shake out obscure memory corruption problems. The freed area is
           still regarded by Memcheck as not valid for access -- this option only affects its
           contents. Note that --free-fill does not affect a block of memory when it is used as
           argument to client requests VALGRIND_MEMPOOL_FREE or VALGRIND_FREELIKE_BLOCK.

CACHEGRIND OPTIONS
       --I1=<size>,<associativity>,<line size>
           Specify the size, associativity and line size of the level 1 instruction cache.

       --D1=<size>,<associativity>,<line size>
           Specify the size, associativity and line size of the level 1 data cache.

       --LL=<size>,<associativity>,<line size>
           Specify the size, associativity and line size of the last-level cache.

       --cache-sim=no|yes [yes]
           Enables or disables collection of cache access and miss counts.

       --branch-sim=no|yes [no]
           Enables or disables collection of branch instruction and misprediction counts. By default
           this is disabled as it slows Cachegrind down by approximately 25%. Note that you cannot
           specify --cache-sim=no and --branch-sim=no together, as that would leave Cachegrind with
           no information to collect.

       --cachegrind-out-file=<file>
           Write the profile data to file rather than to the default output file,
           cachegrind.out.<pid>. The %p and %q format specifiers can be used to embed the process ID
           and/or the contents of an environment variable in the name, as is the case for the core
           option --log-file.

CALLGRIND OPTIONS
       --callgrind-out-file=<file>
           Write the profile data to file rather than to the default output file,
           callgrind.out.<pid>. The %p and %q format specifiers can be used to embed the process ID
           and/or the contents of an environment variable in the name, as is the case for the core
           option --log-file. When multiple dumps are made, the file name is modified further; see
           below.

       --dump-line=<no|yes> [default: yes]
           This specifies that event counting should be performed at source line granularity. This
           allows source annotation for sources which are compiled with debug information (-g).

       --dump-instr=<no|yes> [default: no]
           This specifies that event counting should be performed at per-instruction granularity.
           This allows for assembly code annotation. Currently the results can only be displayed by
           KCachegrind.

       --compress-strings=<no|yes> [default: yes]
           This option influences the output format of the profile data. It specifies whether
           strings (file and function names) should be identified by numbers. This shrinks the file,
           but makes it more difficult for humans to read (which is not recommended in any case).

       --compress-pos=<no|yes> [default: yes]
           This option influences the output format of the profile data. It specifies whether
           numerical positions are always specified as absolute values or are allowed to be relative
           to previous numbers. This shrinks the file size.

       --combine-dumps=<no|yes> [default: no]
           When enabled, when multiple profile data parts are to be generated these parts are
           appended to the same output file. Not recommended.

       --dump-every-bb=<count> [default: 0, never]
           Dump profile data every count basic blocks. Whether a dump is needed is only checked when
           Valgrind's internal scheduler is run. Therefore, the minimum setting useful is about
           100000. The count is a 64-bit value to make long dump periods possible.

       --dump-before=<function>
           Dump when entering function.

       --zero-before=<function>
           Zero all costs when entering function.

       --dump-after=<function>
           Dump when leaving function.

       --instr-atstart=<yes|no> [default: yes]
           Specify if you want Callgrind to start simulation and profiling from the beginning of the
           program. When set to no, Callgrind will not be able to collect any information, including
           calls, but it will have at most a slowdown of around 4, which is the minimum Valgrind
           overhead. Instrumentation can be interactively enabled via callgrind_control -i on.

           Note that the resulting call graph will most probably not contain main, but will contain
           all the functions executed after instrumentation was enabled. Instrumentation can also
           programatically enabled/disabled. See the Callgrind include file callgrind.h for the
           macro you have to use in your source code.

           For cache simulation, results will be less accurate when switching on instrumentation
           later in the program run, as the simulator starts with an empty cache at that moment.
           Switch on event collection later to cope with this error.

       --collect-atstart=<yes|no> [default: yes]
           Specify whether event collection is enabled at beginning of the profile run.

           To only look at parts of your program, you have two possibilities:

            1. Zero event counters before entering the program part you want to profile, and dump
               the event counters to a file after leaving that program part.

            2. Switch on/off collection state as needed to only see event counters happening while
               inside of the program part you want to profile.

           The second option can be used if the program part you want to profile is called many
           times. Option 1, i.e. creating a lot of dumps is not practical here.

           Collection state can be toggled at entry and exit of a given function with the option
           --toggle-collect. If you use this option, collection state should be disabled at the
           beginning. Note that the specification of --toggle-collect implicitly sets
           --collect-state=no.

           Collection state can be toggled also by inserting the client request
           CALLGRIND_TOGGLE_COLLECT ; at the needed code positions.

       --toggle-collect=<function>
           Toggle collection on entry/exit of function.

       --collect-jumps=<no|yes> [default: no]
           This specifies whether information for (conditional) jumps should be collected. As above,
           callgrind_annotate currently is not able to show you the data. You have to use
           KCachegrind to get jump arrows in the annotated code.

       --collect-systime=<no|yes> [default: no]
           This specifies whether information for system call times should be collected.

       --collect-bus=<no|yes> [default: no]
           This specifies whether the number of global bus events executed should be collected. The
           event type "Ge" is used for these events.

       --cache-sim=<yes|no> [default: no]
           Specify if you want to do full cache simulation. By default, only instruction read
           accesses will be counted ("Ir"). With cache simulation, further event counters are
           enabled: Cache misses on instruction reads ("I1mr"/"ILmr"), data read accesses ("Dr") and
           related cache misses ("D1mr"/"DLmr"), data write accesses ("Dw") and related cache misses
           ("D1mw"/"DLmw"). For more information, see Cachegrind: a cache and branch-prediction
           profiler.

       --branch-sim=<yes|no> [default: no]
           Specify if you want to do branch prediction simulation. Further event counters are
           enabled: Number of executed conditional branches and related predictor misses
           ("Bc"/"Bcm"), executed indirect jumps and related misses of the jump address predictor
           ("Bi"/"Bim").

HELGRIND OPTIONS
       --free-is-write=no|yes [default: no]
           When enabled (not the default), Helgrind treats freeing of heap memory as if the memory
           was written immediately before the free. This exposes races where memory is referenced by
           one thread, and freed by another, but there is no observable synchronisation event to
           ensure that the reference happens before the free.

           This functionality is new in Valgrind 3.7.0, and is regarded as experimental. It is not
           enabled by default because its interaction with custom memory allocators is not well
           understood at present. User feedback is welcomed.

       --track-lockorders=no|yes [default: yes]
           When enabled (the default), Helgrind performs lock order consistency checking. For some
           buggy programs, the large number of lock order errors reported can become annoying,
           particularly if you're only interested in race errors. You may therefore find it helpful
           to disable lock order checking.

       --history-level=none|approx|full [default: full]
           --history-level=full (the default) causes Helgrind collects enough information about
           "old" accesses that it can produce two stack traces in a race report -- both the stack
           trace for the current access, and the trace for the older, conflicting access. To limit
           memory usage, "old" accesses stack traces are limited to a maximum of 8 entries, even if
           --num-callers value is bigger.

           Collecting such information is expensive in both speed and memory, particularly for
           programs that do many inter-thread synchronisation events (locks, unlocks, etc). Without
           such information, it is more difficult to track down the root causes of races.
           Nonetheless, you may not need it in situations where you just want to check for the
           presence or absence of races, for example, when doing regression testing of a previously
           race-free program.

           --history-level=none is the opposite extreme. It causes Helgrind not to collect any
           information about previous accesses. This can be dramatically faster than
           --history-level=full.

           --history-level=approx provides a compromise between these two extremes. It causes
           Helgrind to show a full trace for the later access, and approximate information regarding
           the earlier access. This approximate information consists of two stacks, and the earlier
           access is guaranteed to have occurred somewhere between program points denoted by the two
           stacks. This is not as useful as showing the exact stack for the previous access (as
           --history-level=full does), but it is better than nothing, and it is almost as fast as
           --history-level=none.

       --delta-stacktrace=no|yes [default: yes on linux amd64/x86]
           This flag only has any effect at --history-level=full.

           --delta-stacktrace configures the way Helgrind captures the stacktraces for the option
           --history-level=full. Such a stacktrace is typically needed each time a new piece of
           memory is read or written in a basic block of instructions.

           --delta-stacktrace=no causes Helgrind to compute a full history stacktrace from the
           unwind info each time a stacktrace is needed.

           --delta-stacktrace=yes indicates to Helgrind to derive a new stacktrace from the previous
           stacktrace, as long as there was no call instruction, no return instruction, or any other
           instruction changing the call stack since the previous stacktrace was captured. If no
           such instruction was executed, the new stacktrace can be derived from the previous
           stacktrace by just changing the top frame to the current program counter. This option can
           speed up Helgrind by 25% when using --history-level=full.

           The following aspects have to be considered when using --delta-stacktrace=yes :

           ·   In some cases (for example in a function prologue), the
                           valgrind unwinder might not properly unwind the stack, due to some
                           limitations and/or due to wrong unwind info. When using
                           --delta-stacktrace=yes, the wrong stack trace captured in the
                           function prologue will be kept till the next call or return.
                         .RE

               ·   On the other hand, --delta-stacktrace=yes sometimes helps to
                               obtain a correct stacktrace, for example when the unwind info allows
                               a correct stacktrace to be done in the beginning of the sequence,
                               but not later on in the instruction sequence..RE

                   ·   Determining which instructions are changing the callstack is
                                   partially based on platform dependent heuristics, which have to
                       be
                                   tuned/validated specifically for the platform. Also, unwinding in
                       a
                                   function prologue must be good enough to allow using
                                   --delta-stacktrace=yes. Currently, the option
                       --delta-stacktrace=yes
                                   has been reasonably validated only on linux x86 32 bits and linux
                                   amd64 64 bits. For more details about how to validate
                                   --delta-stacktrace=yes, see debug option --hg-sanity-flags and
                       the
                                   function check_cached_rcec_ok in libhb_core.c..RE


                   --conflict-cache-size=N [default: 1000000]
                       This flag only has any effect at --history-level=full.

                       Information about "old" conflicting accesses is stored in a cache of limited
                       size, with LRU-style management. This is necessary because it isn't practical
                       to store a stack trace for every single memory access made by the program.
                       Historical information on not recently accessed locations is periodically
                       discarded, to free up space in the cache.

                       This option controls the size of the cache, in terms of the number of
                       different memory addresses for which conflicting access information is
                       stored. If you find that Helgrind is showing race errors with only one stack
                       instead of the expected two stacks, try increasing this value.

                       The minimum value is 10,000 and the maximum is 30,000,000 (thirty times the
                       default value). Increasing the value by 1 increases Helgrind's memory
                       requirement by very roughly 100 bytes, so the maximum value will easily eat
                       up three extra gigabytes or so of memory.

                   --check-stack-refs=no|yes [default: yes]
                       By default Helgrind checks all data memory accesses made by your program.
                       This flag enables you to skip checking for accesses to thread stacks (local
                       variables). This can improve performance, but comes at the cost of missing
                       races on stack-allocated data.

                   --ignore-thread-creation=<yes|no> [default: no]
                       Controls whether all activities during thread creation should be ignored. By
                       default enabled only on Solaris. Solaris provides higher throughput,
                       parallelism and scalability than other operating systems, at the cost of more
                       fine-grained locking activity. This means for example that when a thread is
                       created under glibc, just one big lock is used for all thread setup. Solaris
                       libc uses several fine-grained locks and the creator thread resumes its
                       activities as soon as possible, leaving for example stack and TLS setup
                       sequence to the created thread. This situation confuses Helgrind as it
                       assumes there is some false ordering in place between creator and created
                       thread; and therefore many types of race conditions in the application would
                       not be reported. To prevent such false ordering, this command line option is
                       set to yes by default on Solaris. All activity (loads, stores, client
                       requests) is therefore ignored during:

                       ·   pthread_create() call in the creator thread

                       ·   thread creation phase (stack and TLS setup) in the created thread

                       Also new memory allocated during thread creation is untracked, that is race
                       reporting is suppressed there. DRD does the same thing implicitly. This is
                       necessary because Solaris libc caches many objects and reuses them for
                       different threads and that confuses Helgrind.

DRD OPTIONS
       --check-stack-var=<yes|no> [default: no]
           Controls whether DRD detects data races on stack variables. Verifying stack variables is
           disabled by default because most programs do not share stack variables over threads.

       --exclusive-threshold=<n> [default: off]
           Print an error message if any mutex or writer lock has been held longer than the time
           specified in milliseconds. This option enables the detection of lock contention.

       --join-list-vol=<n> [default: 10]
           Data races that occur between a statement at the end of one thread and another thread can
           be missed if memory access information is discarded immediately after a thread has been
           joined. This option allows to specify for how many joined threads memory access
           information should be retained.

        --first-race-only=<yes|no> [default: no]
           Whether to report only the first data race that has been detected on a memory location or
           all data races that have been detected on a memory location.

        --free-is-write=<yes|no> [default: no]
           Whether to report races between accessing memory and freeing memory. Enabling this option
           may cause DRD to run slightly slower. Notes:

           ·   Don't enable this option when using custom memory allocators that use the
               VG_USERREQ__MALLOCLIKE_BLOCK and VG_USERREQ__FREELIKE_BLOCK because that would result
               in false positives.

           ·   Don't enable this option when using reference-counted objects because that will
               result in false positives, even when that code has been annotated properly with
               ANNOTATE_HAPPENS_BEFORE and ANNOTATE_HAPPENS_AFTER. See e.g. the output of the
               following command for an example: valgrind --tool=drd --free-is-write=yes
               drd/tests/annotate_smart_pointer.

        --report-signal-unlocked=<yes|no> [default: yes]
           Whether to report calls to pthread_cond_signal and pthread_cond_broadcast where the mutex
           associated with the signal through pthread_cond_wait or pthread_cond_timed_waitis not
           locked at the time the signal is sent. Sending a signal without holding a lock on the
           associated mutex is a common programming error which can cause subtle race conditions and
           unpredictable behavior. There exist some uncommon synchronization patterns however where
           it is safe to send a signal without holding a lock on the associated mutex.

       --segment-merging=<yes|no> [default: yes]
           Controls segment merging. Segment merging is an algorithm to limit memory usage of the
           data race detection algorithm. Disabling segment merging may improve the accuracy of the
           so-called 'other segments' displayed in race reports but can also trigger an out of
           memory error.

       --segment-merging-interval=<n> [default: 10]
           Perform segment merging only after the specified number of new segments have been
           created. This is an advanced configuration option that allows to choose whether to
           minimize DRD's memory usage by choosing a low value or to let DRD run faster by choosing
           a slightly higher value. The optimal value for this parameter depends on the program
           being analyzed. The default value works well for most programs.

       --shared-threshold=<n> [default: off]
           Print an error message if a reader lock has been held longer than the specified time (in
           milliseconds). This option enables the detection of lock contention.

       --show-confl-seg=<yes|no> [default: yes]
           Show conflicting segments in race reports. Since this information can help to find the
           cause of a data race, this option is enabled by default. Disabling this option makes the
           output of DRD more compact.

       --show-stack-usage=<yes|no> [default: no]
           Print stack usage at thread exit time. When a program creates a large number of threads
           it becomes important to limit the amount of virtual memory allocated for thread stacks.
           This option makes it possible to observe how much stack memory has been used by each
           thread of the client program. Note: the DRD tool itself allocates some temporary data on
           the client thread stack. The space necessary for this temporary data must be allocated by
           the client program when it allocates stack memory, but is not included in stack usage
           reported by DRD.

       --ignore-thread-creation=<yes|no> [default: no]
           Controls whether all activities during thread creation should be ignored. By default
           enabled only on Solaris. Solaris provides higher throughput, parallelism and scalability
           than other operating systems, at the cost of more fine-grained locking activity. This
           means for example that when a thread is created under glibc, just one big lock is used
           for all thread setup. Solaris libc uses several fine-grained locks and the creator thread
           resumes its activities as soon as possible, leaving for example stack and TLS setup
           sequence to the created thread. This situation confuses DRD as it assumes there is some
           false ordering in place between creator and created thread; and therefore many types of
           race conditions in the application would not be reported. To prevent such false ordering,
           this command line option is set to yes by default on Solaris. All activity (loads,
           stores, client requests) is therefore ignored during:

           ·   pthread_create() call in the creator thread

           ·   thread creation phase (stack and TLS setup) in the created thread

       --trace-addr=<address> [default: none]
           Trace all load and store activity for the specified address. This option may be specified
           more than once.

       --ptrace-addr=<address> [default: none]
           Trace all load and store activity for the specified address and keep doing that even
           after the memory at that address has been freed and reallocated.

       --trace-alloc=<yes|no> [default: no]
           Trace all memory allocations and deallocations. May produce a huge amount of output.

       --trace-barrier=<yes|no> [default: no]
           Trace all barrier activity.

       --trace-cond=<yes|no> [default: no]
           Trace all condition variable activity.

       --trace-fork-join=<yes|no> [default: no]
           Trace all thread creation and all thread termination events.

       --trace-hb=<yes|no> [default: no]
           Trace execution of the ANNOTATE_HAPPENS_BEFORE(), ANNOTATE_HAPPENS_AFTER() and
           ANNOTATE_HAPPENS_DONE() client requests.

       --trace-mutex=<yes|no> [default: no]
           Trace all mutex activity.

       --trace-rwlock=<yes|no> [default: no]
           Trace all reader-writer lock activity.

       --trace-semaphore=<yes|no> [default: no]
           Trace all semaphore activity.

MASSIF OPTIONS
       --heap=<yes|no> [default: yes]
           Specifies whether heap profiling should be done.

       --heap-admin=<size> [default: 8]
           If heap profiling is enabled, gives the number of administrative bytes per block to use.
           This should be an estimate of the average, since it may vary. For example, the allocator
           used by glibc on Linux requires somewhere between 4 to 15 bytes per block, depending on
           various factors. That allocator also requires admin space for freed blocks, but Massif
           cannot account for this.

       --stacks=<yes|no> [default: no]
           Specifies whether stack profiling should be done. This option slows Massif down greatly,
           and so is off by default. Note that Massif assumes that the main stack has size zero at
           start-up. This is not true, but doing otherwise accurately is difficult. Furthermore,
           starting at zero better indicates the size of the part of the main stack that a user
           program actually has control over.

       --pages-as-heap=<yes|no> [default: no]
           Tells Massif to profile memory at the page level rather than at the malloc'd block level.
           See above for details.

       --depth=<number> [default: 30]
           Maximum depth of the allocation trees recorded for detailed snapshots. Increasing it will
           make Massif run somewhat more slowly, use more memory, and produce bigger output files.

       --alloc-fn=<name>
           Functions specified with this option will be treated as though they were a heap
           allocation function such as malloc. This is useful for functions that are wrappers to
           malloc or new, which can fill up the allocation trees with uninteresting information.
           This option can be specified multiple times on the command line, to name multiple
           functions.

           Note that the named function will only be treated this way if it is the top entry in a
           stack trace, or just below another function treated this way. For example, if you have a
           function malloc1 that wraps malloc, and malloc2 that wraps malloc1, just specifying
           --alloc-fn=malloc2 will have no effect. You need to specify --alloc-fn=malloc1 as well.
           This is a little inconvenient, but the reason is that checking for allocation functions
           is slow, and it saves a lot of time if Massif can stop looking through the stack trace
           entries as soon as it finds one that doesn't match rather than having to continue through
           all the entries.

           Note that C++ names are demangled. Note also that overloaded C++ names must be written in
           full. Single quotes may be necessary to prevent the shell from breaking them up. For
           example:

               --alloc-fn='operator new(unsigned, std::nothrow_t const&)'


       --ignore-fn=<name>
           Any direct heap allocation (i.e. a call to malloc, new, etc, or a call to a function
           named by an --alloc-fn option) that occurs in a function specified by this option will be
           ignored. This is mostly useful for testing purposes. This option can be specified
           multiple times on the command line, to name multiple functions.

           Any realloc of an ignored block will also be ignored, even if the realloc call does not
           occur in an ignored function. This avoids the possibility of negative heap sizes if
           ignored blocks are shrunk with realloc.

           The rules for writing C++ function names are the same as for --alloc-fn above.

       --threshold=<m.n> [default: 1.0]
           The significance threshold for heap allocations, as a percentage of total memory size.
           Allocation tree entries that account for less than this will be aggregated. Note that
           this should be specified in tandem with ms_print's option of the same name.

       --peak-inaccuracy=<m.n> [default: 1.0]
           Massif does not necessarily record the actual global memory allocation peak; by default
           it records a peak only when the global memory allocation size exceeds the previous peak
           by at least 1.0%. This is because there can be many local allocation peaks along the way,
           and doing a detailed snapshot for every one would be expensive and wasteful, as all but
           one of them will be later discarded. This inaccuracy can be changed (even to 0.0%) via
           this option, but Massif will run drastically slower as the number approaches zero.

       --time-unit=<i|ms|B> [default: i]
           The time unit used for the profiling. There are three possibilities: instructions
           executed (i), which is good for most cases; real (wallclock) time (ms, i.e.
           milliseconds), which is sometimes useful; and bytes allocated/deallocated on the heap
           and/or stack (B), which is useful for very short-run programs, and for testing purposes,
           because it is the most reproducible across different machines.

       --detailed-freq=<n> [default: 10]
           Frequency of detailed snapshots. With --detailed-freq=1, every snapshot is detailed.

       --max-snapshots=<n> [default: 100]
           The maximum number of snapshots recorded. If set to N, for all programs except very
           short-running ones, the final number of snapshots will be between N/2 and N.

       --massif-out-file=<file> [default: massif.out.%p]
           Write the profile data to file rather than to the default output file, massif.out.<pid>.
           The %p and %q format specifiers can be used to embed the process ID and/or the contents
           of an environment variable in the name, as is the case for the core option --log-file.

SGCHECK OPTIONS
       There are no SGCheck-specific command-line options at present.

BBV OPTIONS
       --bb-out-file=<name> [default: bb.out.%p]
           This option selects the name of the basic block vector file. The %p and %q format
           specifiers can be used to embed the process ID and/or the contents of an environment
           variable in the name, as is the case for the core option --log-file.

       --pc-out-file=<name> [default: pc.out.%p]
           This option selects the name of the PC file. This file holds program counter addresses
           and function name info for the various basic blocks. This can be used in conjunction with
           the basic block vector file to fast-forward via function names instead of just
           instruction counts. The %p and %q format specifiers can be used to embed the process ID
           and/or the contents of an environment variable in the name, as is the case for the core
           option --log-file.

       --interval-size=<number> [default: 100000000]
           This option selects the size of the interval to use. The default is 100 million
           instructions, which is a commonly used value. Other sizes can be used; smaller intervals
           can help programs with finer-grained phases. However smaller interval size can lead to
           accuracy issues due to warm-up effects (When fast-forwarding the various architectural
           features will be un-initialized, and it will take some number of instructions before they
           "warm up" to the state a full simulation would be at without the fast-forwarding. Large
           interval sizes tend to mitigate this.)

       --instr-count-only [default: no]
           This option tells the tool to only display instruction count totals, and to not generate
           the actual basic block vector file. This is useful for debugging, and for gathering
           instruction count info without generating the large basic block vector files.

LACKEY OPTIONS
       --basic-counts=<no|yes> [default: yes]
           When enabled, Lackey prints the following statistics and information about the execution
           of the client program:

            1. The number of calls to the function specified by the --fnname option (the default is
               main). If the program has had its symbols stripped, the count will always be zero.

            2. The number of conditional branches encountered and the number and proportion of those
               taken.

            3. The number of superblocks entered and completed by the program. Note that due to
               optimisations done by the JIT, this is not at all an accurate value.

            4. The number of guest (x86, amd64, ppc, etc.) instructions and IR statements executed.
               IR is Valgrind's RISC-like intermediate representation via which all instrumentation
               is done.

            5. Ratios between some of these counts.

            6. The exit code of the client program.

       --detailed-counts=<no|yes> [default: no]
           When enabled, Lackey prints a table containing counts of loads, stores and ALU
           operations, differentiated by their IR types. The IR types are identified by their IR
           name ("I1", "I8", ... "I128", "F32", "F64", and "V128").

       --trace-mem=<no|yes> [default: no]
           When enabled, Lackey prints the size and address of almost every memory access made by
           the program. See the comments at the top of the file lackey/lk_main.c for details about
           the output format, how it works, and inaccuracies in the address trace. Note that this
           option produces immense amounts of output.

       --trace-superblocks=<no|yes> [default: no]
           When enabled, Lackey prints out the address of every superblock (a single entry, multiple
           exit, linear chunk of code) executed by the program. This is primarily of interest to
           Valgrind developers. See the comments at the top of the file lackey/lk_main.c for details
           about the output format. Note that this option produces large amounts of output.

       --fnname=<name> [default: main]
           Changes the function for which calls are counted when --basic-counts=yes is specified.

SEE ALSO
       cg_annotate(1), callgrind_annotate(1), callgrind_control(1), ms_print(1),
       $INSTALL/share/doc/valgrind/html/index.html or
       http://www.valgrind.org/docs/manual/index.html, Debugging your program using Valgrind's
       gdbserver and GDB[1] vgdb[2], Valgrind monitor commands[3], The Commentary[4], Scheduling and
       Multi-Thread Performance[5], Cachegrind: a cache and branch-prediction profiler[6].

AUTHOR
       See the AUTHORS file in the valgrind distribution for a comprehensive list of authors.

       This manpage was written by Andres Roldan <aroldan@debian.org> and the Valgrind developers.

NOTES
        1. Debugging your program using Valgrind's gdbserver and GDB
           http://www.valgrind.org/docs/manual/manual-core-adv.html#manual-core-adv.gdbserver

        2. vgdb
           http://www.valgrind.org/docs/manual/manual-core-adv.html#manual-core-adv.vgdb

        3. Valgrind monitor commands
           http://www.valgrind.org/docs/manual/manual-core-adv.html#manual-core-adv.valgrind-monitor-commands

        4. The Commentary
           http://www.valgrind.org/docs/manual/manual-core.html#manual-core.comment

        5. Scheduling and Multi-Thread Performance
           http://www.valgrind.org/docs/manual/manual-core.html#manual-core.pthreads_perf_sched

        6. Cachegrind: a cache and branch-prediction profiler
           http://www.valgrind.org/docs/manual/cg-manual.html



Release 3.15.0                               04/13/2019                                  VALGRIND(1)
```

### 6.1 介绍

#### 6.1.1 memcheck

#### 6.1.2 callgrind

#### 6.1.3 helgrind

#### 6.1.4 cachegrind

#### 6.1.5 massif

#### 6.1.6 extension



### 6.3 实践

"definitely lost"：确认丢失。程序中存在内存泄露，应尽快修复。当程序结束时如果一块动态分配的内存没有被释放且通过程序内的指针变量均无法访问这块内存则会报这个错误。

"indirectly lost"：间接丢失。当使用了含有指针成员的类或结构时可能会报这个错误。这类错误无需直接修复，他们总是与"definitely lost"一起出现，只要修复"definitely lost"即可。例子可参考我的例程。

"possibly lost"：可能丢失。大多数情况下应视为与"definitely lost"一样需要尽快修复，除非你的程序让一个指针指向一块动态分配的内存（但不是这块内存起始地址），然后通过运算得到这块内存起始地址，再释放它。例子可参考我的例程。当程序结束时如果一块动态分配的内存没有被释放且通过程序内的指针变量均无法访问这块内存的起始地址，但可以访问其中的某一部分数据，则会报这个错误。

"still reachable"：可以访问，未丢失但也未释放。如果程序是正常结束的，那么它可能不会造成程序崩溃，但长时间运行有可能耗尽系统资源，因此笔者建议修复它。如果程序是崩溃（如访问非法的地址而崩溃）而非正常结束的，则应当暂时忽略它，先修复导致程序崩溃的错误，然后重新检测。

"suppressed"：已被解决。出现了内存泄露但系统自动处理了。可以无视这类错误。这类错误我没能用例程触发，看官方的解释也不太清楚是操作系统处理的还是valgrind，也没有遇到过。所以无视他吧~

```bash
$ valgrind --log-file="report.log" --tool=memcheck --leak-check=full --show-leak-kinds=all  exe
```

#### 6.3.1 内存读写越界

```cpp
// 加上 -g valgrind 输出中会定位到源代码行
// g++ -g dm01.cpp
#include <iostream>
#include <stdlib.h>

using namespace std;

void func()
{
    int *x = (int *)malloc(10 * sizeof(int));
    x[10] = 10;
    
    free(x);
}

int main()
{
    func();
    return 0;
}
```

```shell
$ valgrind --leak-check=full ./a.out
==10266== Memcheck, a memory error detector
==10266== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==10266== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==10266== Command: ./a.out
==10266== 
==10266== Invalid write of size 4
==10266==    at 0x4008F1: func() (dm01.cpp:9)
==10266==    by 0x400930: main (dm01.cpp:17)
==10266==  Address 0x5a25068 is 0 bytes after a block of size 40 alloc'd
==10266==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==10266==    by 0x4008E4: func() (dm01.cpp:8)
==10266==    by 0x400930: main (dm01.cpp:17)
==10266== 
==10266== Invalid read of size 4
==10266==    at 0x4008FF: func() (dm01.cpp:11)
==10266==    by 0x400930: main (dm01.cpp:17)
==10266==  Address 0x5a25068 is 0 bytes after a block of size 40 alloc'd
==10266==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==10266==    by 0x4008E4: func() (dm01.cpp:8)
==10266==    by 0x400930: main (dm01.cpp:17)
==10266== 
10
==10266== 
==10266== HEAP SUMMARY:
==10266==     in use at exit: 0 bytes in 0 blocks
==10266==   total heap usage: 1 allocs, 1 frees, 40 bytes allocated
==10266== 
==10266== All heap blocks were freed -- no leaks are possible
==10266== 
==10266== For lists of detected and suppressed errors, rerun with: -s
==10266== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
```

#### 6.3.2 使用未初始化的内存

```cpp
// 加上 -g valgrind 输出中会定位到源代码行
// g++ -g dm02.cpp
#include <iostream>
#include <stdlib.h>

using namespace std;

void func()
{
    int *x = (int *)malloc(5 * sizeof(int));
    x[0] = x[1] = x[2] = x[3] = 0;

    int a = x[4];

    cout << a << endl;
    free(x);
}

int main()
{
    func();
    return 0;
}
```

```shell
$ valgrind --leak-check=full --track-origins=yes ./a.out 
==14000== Memcheck, a memory error detector
==14000== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==14000== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==14000== Command: ./a.out
==14000== 
==14000== Conditional jump or move depends on uninitialised value(s)
==14000==    at 0x4EC171E: std::ostreambuf_iterator<char, std::char_traits<char> > std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::_M_insert_int<long>(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4EC1CFC: std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::do_put(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4ECE06D: std::ostream& std::ostream::_M_insert<long>(long) (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x40092F: func() (dm02.cpp:13)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000==  Uninitialised value was created by a heap allocation
==14000==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==14000==    by 0x4008E4: func() (dm02.cpp:8)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000== 
==14000== Use of uninitialised value of size 8 -- 为什么是8字节
==14000==    at 0x4EC1603: ??? (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4EC1745: std::ostreambuf_iterator<char, std::char_traits<char> > std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::_M_insert_int<long>(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4EC1CFC: std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::do_put(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4ECE06D: std::ostream& std::ostream::_M_insert<long>(long) (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x40092F: func() (dm02.cpp:13)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000==  Uninitialised value was created by a heap allocation
==14000==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==14000==    by 0x4008E4: func() (dm02.cpp:8)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000== 
==14000== Conditional jump or move depends on uninitialised value(s)
==14000==    at 0x4EC160F: ??? (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4EC1745: std::ostreambuf_iterator<char, std::char_traits<char> > std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::_M_insert_int<long>(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4EC1CFC: std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::do_put(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4ECE06D: std::ostream& std::ostream::_M_insert<long>(long) (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x40092F: func() (dm02.cpp:13)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000==  Uninitialised value was created by a heap allocation
==14000==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==14000==    by 0x4008E4: func() (dm02.cpp:8)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000== 
==14000== Conditional jump or move depends on uninitialised value(s)
==14000==    at 0x4EC1773: std::ostreambuf_iterator<char, std::char_traits<char> > std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::_M_insert_int<long>(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4EC1CFC: std::num_put<char, std::ostreambuf_iterator<char, std::char_traits<char> > >::do_put(std::ostreambuf_iterator<char, std::char_traits<char> >, std::ios_base&, char, long) const (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x4ECE06D: std::ostream& std::ostream::_M_insert<long>(long) (in /usr/lib64/libstdc++.so.6.0.19)
==14000==    by 0x40092F: func() (dm02.cpp:13)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000==  Uninitialised value was created by a heap allocation
==14000==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==14000==    by 0x4008E4: func() (dm02.cpp:8)
==14000==    by 0x400953: main (dm02.cpp:19)
==14000== 
0
==14000== 
==14000== HEAP SUMMARY:
==14000==     in use at exit: 0 bytes in 0 blocks
==14000==   total heap usage: 1 allocs, 1 frees, 20 bytes allocated
==14000== 
==14000== All heap blocks were freed -- no leaks are possible
==14000== 
==14000== For lists of detected and suppressed errors, rerun with: -s
==14000== ERROR SUMMARY: 4 errors from 4 contexts (suppressed: 0 from 0)
```

#### 6.3.3 内存覆盖

```cpp
// 加上 -g valgrind 输出中会定位到源代码行
// g++ -g dm03.cpp
#include <iostream>
#include <stdlib.h>
#include <string.h>

using namespace std;

int main()
{
    char x[50];
    int i;

    for (i = 0; i < 50; i++)
    {
        x[i] = i + 1;
    }

    strncpy(x + 20, x, 20);
    strncpy(x + 20, x, 21);
    strncpy(x, x + 20, 20);
    strncpy(x, x + 20, 21);

    x[39] = '\0';
    strcpy(x, x + 20);

    x[39] = 39;
    x[40] = '\0';
    strcpy(x, x + 20);

    return 0;
}
```

```shell
$ valgrind --leak-check=full ./a.out
==10803== Memcheck, a memory error detector
==10803== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==10803== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==10803== Command: ./a.out
==10803== 
==10803== Source and destination overlap in strncpy(0x1fff000009, 0x1ffefffff5, 21)
==10803==    at 0x4C2D843: __strncpy_sse2_unaligned (vg_replace_strmem.c:555)
==10803==    by 0x4007D3: main (dm03.cpp:18)
==10803== 
==10803== Source and destination overlap in strncpy(0x1ffefffff5, 0x1fff000009, 21)
==10803==    at 0x4C2D843: __strncpy_sse2_unaligned (vg_replace_strmem.c:555)
==10803==    by 0x40080B: main (dm03.cpp:20)
==10803== 
==10803== Source and destination overlap in strcpy(0x1ffeffffe0, 0x1ffefffff4)
==10803==    at 0x4C2D282: strcpy (vg_replace_strmem.c:513)
==10803==    by 0x400845: main (dm03.cpp:27)
==10803== 
==10803== 
==10803== HEAP SUMMARY:
==10803==     in use at exit: 0 bytes in 0 blocks
==10803==   total heap usage: 0 allocs, 0 frees, 0 bytes allocated
==10803== 
==10803== All heap blocks were freed -- no leaks are possible
==10803== 
==10803== For lists of detected and suppressed errors, rerun with: -s
==10803== ERROR SUMMARY: 3 errors from 3 contexts (suppressed: 0 from 0)
```

#### 6.3.4 动态内存管理错误

* 申请和释放不一致，使用申请和释放内存的接口不匹配
* 申请和释放不匹配，多释放或少释放
* 释放后仍然继续访问

```c++
// 加上 -g valgrind 输出中会定位到源代码行
// g++ -g dm04.cpp
#include <iostream>
#include <stdlib.h>

using namespace std;

int main()
{
    int *x = (int *)malloc(5 * sizeof(int));
    int i;

    for (i = 0; i < 5; i++)
    {
        x[i] = i + 1;
    }

    delete x;
    x[0] = 1;
    free(x);

    return 0;
}
```

```shell
$ valgrind --leak-check=full ./a.out
==10902== Command: ./a.out
==10902== 
==10902== Mismatched free() / delete / delete []
==10902==    at 0x4C2B51D: operator delete(void*) (vg_replace_malloc.c:586)
==10902==    by 0x400813: main (dm04.cpp:16)
==10902==  Address 0x5a25040 is 0 bytes inside a block of size 20 alloc'd
==10902==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==10902==    by 0x4007D4: main (dm04.cpp:8)
==10902== 
==10902== Invalid write of size 4
==10902==    at 0x400818: main (dm04.cpp:17)
==10902==  Address 0x5a25040 is 0 bytes inside a block of size 20 free'd
==10902==    at 0x4C2B51D: operator delete(void*) (vg_replace_malloc.c:586)
==10902==    by 0x400813: main (dm04.cpp:16)
==10902==  Block was alloc'd at
==10902==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==10902==    by 0x4007D4: main (dm04.cpp:8)
==10902== 
==10902== Invalid free() / delete / delete[] / realloc()
==10902==    at 0x4C2B06D: free (vg_replace_malloc.c:540)
==10902==    by 0x400829: main (dm04.cpp:18)
==10902==  Address 0x5a25040 is 0 bytes inside a block of size 20 free'd
==10902==    at 0x4C2B51D: operator delete(void*) (vg_replace_malloc.c:586)
==10902==    by 0x400813: main (dm04.cpp:16)
==10902==  Block was alloc'd at
==10902==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==10902==    by 0x4007D4: main (dm04.cpp:8)
==10902== 
==10902== 
==10902== HEAP SUMMARY:
==10902==     in use at exit: 0 bytes in 0 blocks
==10902==   total heap usage: 1 allocs, 2 frees, 20 bytes allocated
==10902== 
==10902== All heap blocks were freed -- no leaks are possible
==10902== 
==10902== For lists of detected and suppressed errors, rerun with: -s
==10902== ERROR SUMMARY: 3 errors from 3 contexts (suppressed: 0 from 0)
```

#### 6.3.5 内存泄露

​	"definitely lost" means your program is leaking memory -- fix those leaks!


​	"indirectly lost" means your program is leaking memory in a pointer-based structure. (E.g. if the root node of a binary tree is "definitely lost", all the children will be "indirectly lost".) If you fix the "definitely lost" leaks, the "indirectly lost" leaks should go away.


​	"possibly lost" means your program is leaking memory, unless you're doing unusual things with pointers that could cause them to point into the middle of an allocated block; see the user manual for some possible causes. Use --show-possibly-lost=no if you don't want to see these reports.

​	"still reachable" means your is probably ok -- it didn't free some memory it could have. This is quite common and often reasonable. Don't use --show-reachable=yes if you don't want to see these reports.


​	"suppressed" means that a leak error has been suppressed. There are some suppressions in the default suppression files. You can ignore suppressed errors.

* possibly lost：指仍然存在某个指针能够访问某块内存，但是该指针指向的已经不是该内存的首地址
* definitely lost：已经不能访问到的内存
  * direct：没有任何指针指向该内存
  * indirect: 指向该内存的指针都位于内存泄漏处

##### 示例一

```cpp
// 加上 -g valgrind 输出中会定位到源代码行
// g++ -g dm05.cpp
#include <iostream>
#include <stdlib.h>

typedef struct _node
{
    struct _node *l;
    struct _node *r;
    char v;
} node;

node *mknode(node *l, node *r, char val)
{
    node *f = (node *)malloc(sizeof(*f));
    f->l = l;
    f->r = r;
    f->v = val;

    return f;
}

void frnode(node *n)
{
    if (n != NULL)
    {
        frnode(n->l);
        frnode(n->r);
        free(n);
    }
}

int main()
{
    node *tree01, *tree02, *tree03;

    tree01 = mknode(mknode(mknode(NULL, NULL, '3'), NULL, '2'), NULL, '1');
    tree02 = mknode(NULL, mknode(NULL, mknode(NULL, NULL, '6'), '5'), '4');
    tree03 = mknode(mknode(tree01, tree02, '8'), NULL, '7');

    return 0;
}
```

```shell
$ valgrind --leak-check=full ./a.out
==11524== Memcheck, a memory error detector
==11524== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==11524== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==11524== Command: ./a.out
==11524== 
==11524== 
==11524== HEAP SUMMARY:
==11524==     in use at exit: 192 bytes in 8 blocks
==11524==   total heap usage: 8 allocs, 0 frees, 192 bytes allocated
==11524== 
==11524== 192 (24 direct, 168 indirect) bytes in 1 blocks are definitely lost in loss record 8 of 8
==11524==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==11524==    by 0x40078E: mknode(_node*, _node*, char) (dm05.cpp:13)
==11524==    by 0x4008A4: main (dm05.cpp:37)
==11524== 
==11524== LEAK SUMMARY:
==11524==    definitely lost: 24 bytes in 1 blocks
==11524==    indirectly lost: 168 bytes in 7 blocks
==11524==      possibly lost: 0 bytes in 0 blocks
==11524==    still reachable: 0 bytes in 0 blocks
==11524==         suppressed: 0 bytes in 0 blocks
==11524== 
==11524== For lists of detected and suppressed errors, rerun with: -s
==11524== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

##### 示例二

```cpp
// 加上 -g valgrind 输出中会定位到源代码行
// g++ -g dm06.cpp
#include <iostream>
#include <stdlib.h>

using namespace std;

void func00()
{
    static char *s_pcTemp;
    char *pcData;

    pcData = (char *)malloc(10 * sizeof(char));
    s_pcTemp = pcData + 1;
}

char *func01() // possibly lost
{
    static char *s_pcTemp;
    char *pcData;

    pcData = (char *)malloc(10 * sizeof(char));
    s_pcTemp = pcData + 1;
    // pcData = pcData + 1; // 这样还是 definitely lost

    return NULL;
}

// possibly lost but no need of repair,repair the breakdown then no memory leak
char *func02()
{
    char *pcData;
    int i, *piTemp = NULL;

    pcData = (char *)malloc(10);
    pcData += 10;

    for (i = 0; i < 10; i++)
    {
        pcData--;
        *pcData = 0;

        // create a breakdown (core) 会产生 possibly lost
        if (i == 5)
            *piTemp = 1;
    }

    free(pcData);
    return NULL;
}

int main()
{
    func00();
    func01();
    func02();
    return 0;
}
```

```shell
$ valgrind --leak-check=full --show-leak-kinds=all ./a.out 
==13889== Memcheck, a memory error detector
==13889== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==13889== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==13889== Command: ./a.out
==13889== 
==13889== Invalid write of size 4
==13889==    at 0x40080C: func02() (dm06.cpp:45)
==13889==    by 0x400841: main (dm06.cpp:57)
==13889==  Address 0x0 is not stack'd, malloc'd or (recently) free'd
==13889== 
==13889== 
==13889== Process terminating with default action of signal 11 (SIGSEGV): dumping core
==13889==  Access not within mapped region at address 0x0
==13889==    at 0x40080C: func02() (dm06.cpp:45)
==13889==    by 0x400841: main (dm06.cpp:57)
==13889==  If you believe this happened as a result of a stack
==13889==  overflow in your program's main thread (unlikely but
==13889==  possible), you can try to increase the size of the
==13889==  main thread stack using the --main-stacksize= flag.
==13889==  The main thread stack size used in this run was 8388608.
==13889== 
==13889== HEAP SUMMARY:
==13889==     in use at exit: 30 bytes in 3 blocks
==13889==   total heap usage: 3 allocs, 0 frees, 30 bytes allocated
==13889== 
==13889== 10 bytes in 1 blocks are still reachable in loss record 1 of 3
==13889==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==13889==    by 0x400784: func00() (dm06.cpp:11)
==13889==    by 0x400837: main (dm06.cpp:55)
==13889== 
==13889== 10 bytes in 1 blocks are possibly lost in loss record 2 of 3
==13889==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==13889==    by 0x4007AF: func01() (dm06.cpp:22)
==13889==    by 0x40083C: main (dm06.cpp:56)
==13889== 
==13889== 10 bytes in 1 blocks are possibly lost in loss record 3 of 3
==13889==    at 0x4C29F73: malloc (vg_replace_malloc.c:309)
==13889==    by 0x4007E3: func02() (dm06.cpp:35)
==13889==    by 0x400841: main (dm06.cpp:57)
==13889== 
==13889== LEAK SUMMARY:
==13889==    definitely lost: 0 bytes in 0 blocks
==13889==    indirectly lost: 0 bytes in 0 blocks
==13889==      possibly lost: 20 bytes in 2 blocks
==13889==    still reachable: 10 bytes in 1 blocks
==13889==         suppressed: 0 bytes in 0 blocks
==13889== 
==13889== For lists of detected and suppressed errors, rerun with: -s
==13889== ERROR SUMMARY: 3 errors from 3 contexts (suppressed: 0 from 0)
Segmentation fault
```

