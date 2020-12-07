# Build Tool



## 



## GDB 调试

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

```bash
# 设置显示格式
set print pretty on
```

```bash
ptype 
```



### 打印vector的多个元素

```bash
# 打印多个元素
print *(your_vector._M_impl._M_start)@your_vector_size

#打印单个元素[n指下标]
print *(your_vector._M_impl._M_start+n)
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

```bash
info threads 查看当前进程的线程
thread <ID>  切换调试的线程为指定ID的线程
break test.c:100 thread all   在所有线程中相应的行上设置断点

set scheduler-locking off|on
  off   默认值，不锁定任何线程，所有线程都执行
  on    只有当前被调试程序会执行
```

### 多进程



###  问题

#### 打印出的字符串内容不全

```bash
(gdb) show print elements    #查看最大打印字符数
(gdb) set print elements 20000 #设置为20000
```

## GNU (gcc & g++)

```
-L: 指定依赖链接库路径
-l: 指定链接库
-I: 
```

### 1. g++简介

g++是GNU开发的C++编译器，是GCC（GNU Compiler Collection）GNU编译器套件的组成部分。另外，gcc是GNU的C编译器。

看官方手册你会发现g++的命令选项真的多如繁星，令人头皮发麻。但是常用的命令选项也就那几个，完成我们的日常编译，g++使用起来还是比较简单的！g++编译器是GCC的一部分，GCC编译工作一般分为四个步骤：  （1）预处理（Preprocessing）。由预处理器cpp完成，将.cpp源文件预处理为.i文件。

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

### 2.命令格式

```javascript
gcc [-c|-S|-E] [-std=standard]
    [-g] [-pg] [-Olevel]
    [-Wwarn...] [-pedantic]
    [-Idir...] [-Ldir...]
    [-Dmacro[=defn]...] [-Umacro]
    [-foption...] [-mmachine-option...]
    [-o outfile] [@file] infile...
```

### 3.命令选项

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

```javascript
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

### 4.FAQ

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

[1][gcc及其选项详解](http://blog.chinaunix.net/uid-25119314-id-224398.html)  [2][GCC官方手册](https://gcc.gnu.org/onlinedocs/gcc-6.1.0/gcc.pdf)  [3][gcc编译选项](http://www.cnblogs.com/fengbeihong/p/3641384.html)  [4][gcc/g++ 静态动态库混链接](http://blog.csdn.net/wangxvfeng101/article/details/15336955)  [5][折腾gcc/g++链接时.o文件及库的顺序问题](http://blog.csdn.net/imilli/article/details/51454236)  [6][g++参数介绍](http://www.cnblogs.com/lidan/archive/2011/05/25/2239517.html)  [7][gcc cannot find cc1plus](https://stackoverflow.com/questions/36353302/gcc-cannot-find-cc1plus?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

## Makefile

```makefile

$^: 代表所有依赖项
$@: 生成目标
```



## CMake

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

### 新建build目录执行cmake构建

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

## 内存泄露分析

### pmap

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

### gdb dump

```bash
(gdb) dump memory ./dump.bin 0x7f4f64000000 7f4f68000000

# 找到dump出来的文件
$ strings dump.bin
```

### valgrind

"definitely lost"：确认丢失。程序中存在内存泄露，应尽快修复。当程序结束时如果一块动态分配的内存没有被释放且通过程序内的指针变量均无法访问这块内存则会报这个错误。

"indirectly lost"：间接丢失。当使用了含有指针成员的类或结构时可能会报这个错误。这类错误无需直接修复，他们总是与"definitely lost"一起出现，只要修复"definitely lost"即可。例子可参考我的例程。

"possibly lost"：可能丢失。大多数情况下应视为与"definitely lost"一样需要尽快修复，除非你的程序让一个指针指向一块动态分配的内存（但不是这块内存起始地址），然后通过运算得到这块内存起始地址，再释放它。例子可参考我的例程。当程序结束时如果一块动态分配的内存没有被释放且通过程序内的指针变量均无法访问这块内存的起始地址，但可以访问其中的某一部分数据，则会报这个错误。

"still reachable"：可以访问，未丢失但也未释放。如果程序是正常结束的，那么它可能不会造成程序崩溃，但长时间运行有可能耗尽系统资源，因此笔者建议修复它。如果程序是崩溃（如访问非法的地址而崩溃）而非正常结束的，则应当暂时忽略它，先修复导致程序崩溃的错误，然后重新检测。

"suppressed"：已被解决。出现了内存泄露但系统自动处理了。可以无视这类错误。这类错误我没能用例程触发，看官方的解释也不太清楚是操作系统处理的还是valgrind，也没有遇到过。所以无视他吧~

```bash
$ valgrind --log-file="report.log" --tool=memcheck --leak-check=full --show-leak-kinds=all  exe
```

