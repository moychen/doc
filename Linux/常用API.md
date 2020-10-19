# 常用API

## 解析argv参数

### getopt()

```c
#include <unistd.h>

int getopt(int argc,char * const argv[ ],const char * optstring);

extern char *optarg;
extern int optind, opterr, optopt;
```

所设置的全局变量包括：

- `optarg`：指向当前选项参数（如果有）的指针。
- `optind`：再次调用 getopt() 时的下一个 argv 指针的索引。
- `optopt`：最后一个未知选项。

参数 argc 和 argv 分别代表参数个数和内容，跟 main() 函数的命令行参数是一样的。参数 optstring 为选项字符串， 告知 getopt() 可以处理哪个选项以及哪个选项需要参数，如果选项字符串里的字母后接着冒号“:”，则表示还有相关的参数，全域变量optarg 即会指向此额外参数。如果在处理期间遇到了不符合optstring指定的其他选项 getopt() 将显示一个错误消息，并将全域变量 optarg 设为 “?” 字符，如果不希望 getopt() 打印出错信息，则只要将全域变量 opterr 设为 0 即可。

optstring中的指定的内容的意义（例如getopt(argc, argv, “ab:c:de::”);）

- 单个字符，表示选项，（如上例中的abcde各为一个选项）
- 单个字符后接一个冒号：表示该选项后必须跟一个参数。参数紧跟在选项后或者以空格隔开。该参数的指针赋给optarg。
- 单个字符后跟两个冒号，表示该选项后可以跟一个参数，也可以不跟。如果跟一个参数，参数必须紧跟在选项后不能以空格隔开。该参数的指针赋给optarg。

### getopt_long()

getopt_long 支持长选项的命令行解析。

```c
int getopt_long(int argc, char * const argv[],
                const char *optstring,
                const struct option *longopts,
                int *longindex);
```

### getopt_long_only()



## 文件相关

### 获取文件状态

```c
#include < sys / types.h >
#include < sys / stat.h >
#include < unistd.h >

int stat（const char * path ，struct stat * buf ）;
int fstat（int fd ，struct stat * buf ）;
int lstat（const char * path ，struct stat * buf ）;
```

这些函数返回有关文件的信息。文件本身不需要权限，但对于**stat**（）和**lstat**（）而言- 导致文件的*路径*中的所有目录都需要执行（搜索）权限。

* stat() 统计*path*指向的文件并填写*buf*。

* lstat() 与 stat() 相同，不同之处在于，如果*path*是符号链接，则该链接本身是stat-ed，而不是其引用的文件。

* fstat() 与 stat() 相同，除了要通过文件描述符*fd*指定要声明的文件。

所有这些系统调用都返回一个*stat*结构，其中包含以下字段：

```c
struct stat {
    dev_t st_dev; / *包含文件的设备的ID * /
    ino_t st_ino; / *索引节点号* /
    mode_t st_mode; / *保护* /
    nlink_t st_nlink; / *硬链接数* /
    uid_t st_uid; / *所有者的用户ID * /
    gid_t st_gid; / *所有者的组ID * /
    dev_t st_rdev; / *设备ID（如果是特殊文件）* /
    off_t st_size; / *总大小，以字节为单位* /
    blksize_t st_blksize; / *用于文件系统I / O的块大小* /
    blkcnt_t st_blocks; / *分配的512B块数* /
    time_t st_atime; / *上次访问时间* /
    time_t st_mtime; / *上次修改时间* /
    time_t st_ctime; / *上次状态更改的时间* /
};
```

st_blocks字段指示以512字节为单位分配给文件的块数。  (This may be smaller than *st_size*/512 when the file has holes.)

st_blksize字段为有效的文件系统I / O提供“首选”块大小。 （以较小的块写入文件可能会导致读取-修改-重写效率低下。）

### rename

     named（）重命名文件，并根据需要在目录之间移动文件。指向文件的其他任何硬链接（使用link（2）创建）不受影响。 oldpath的打开文件描述符也不受影响。
    各种限制决定了重命名操作是否
           成功：请参见下面的错误。
    
           如果newpath已经存在，它将被原子替换，这样
           没有其他进程尝试访问的点
           newpath会发现它不存在。但是，可能会有一个
           老路径和新路径都在其中引用文件的窗口
           重命名。
    
           如果oldpath和newpath是引用相同路径的现有硬链接
           文件，然后rename（）不执行任何操作，并返回成功状态。
    
           如果存在newpath但由于某种原因操作失败，请重命名（）
           保证将newpath实例保留在原位。
    
           oldpath可以指定目录。在这种情况下，newpath必须
           不存在，或者必须指定一个空目录。
    
           如果oldpath引用符号链接，则该链接将重命名；否则，将重新命名该链接。如果newpath
           指代符号链接，该链接将被覆盖。
## 动态库

```C++
#include <dlfcn.h>

//! 
void *dlopen(const char *filename, int flag);
char *dlerror(void);
void *dlsym(void *handle, const char *symbol);
int dlclose(void *handle);
```

## 信号

```C++
#include <signal.h>

int sigemptyset(sigset_t *set);
int sigfillset(sigset_t *set);
int sigaddset(sigset_t *set, int signum);
int sigdelset(sigset_t *set, int signum);
int sigismember(const sigset_t *set, int signum);
```



## 零拷贝

## 异步IO

## IO复用

## 线程和进程

### 进程



#### fork()



## 网络相关

```C++
//man  2
#include<sys/socket.h>

//用于获取与某个套接字关联的本地协议地址
int getsockname(int sockfd, struct sockaddr *localaddr, socklen_t *addrlen);
//用于获取与某个套接字关联的外地协议地址
int getpeername(int sockfd, struct sockaddr *peeraddr, socklen_t *addrlen);
```

```C++
// man 2
#include <unistd.h> 

//返回字符数组名称中以NULL结尾的主机名，该主机名的长度为len个字节。如果以空值结尾的主机名太大而无法容纳，则该名称将被截断，并且不会返回错误（但请参见下面的注释）。 POSIX.1说，如果发生这种截断，则不确定返回的缓冲区是否包含终止的空字节。
int gethostname(char *name, size_t len);
//将主机名设置为字符数组名称中给定的值。 len参数指定名称中的字节数。 （因此，名称不需要终止的空字节。）
int sethostname(const char *name, size_t len);
```

```C++
// man 3
#include <sys/types.h>
#include <ifaddrs.h>

/*
getifaddrs（）函数创建描述本地系统网络接口的结构的链接列表，并将列表第一项的地址存储在* ifap中。该列表由ifaddrs结构组成，定义如下：
struct ifaddrs {
	struct ifaddrs  *ifa_next;    /* Next item in list */
    char            *ifa_name;    /* Name of interface */
    unsigned int     ifa_flags;   /* Flags from SIOCGIFFLAGS */
    struct sockaddr *ifa_addr;    /* Address of interface */
    struct sockaddr *ifa_netmask; /* Netmask of interface */
    union {
   		struct sockaddr *ifu_broadaddr; /* Broadcast address of interface */
     	struct sockaddr *ifu_dstaddr;   /* Point-to-point destination address */
   	} ifa_ifu;
    
	#define              ifa_broadaddr ifa_ifu.ifu_broadaddr
    #define              ifa_dstaddr   ifa_ifu.ifu_dstaddr
    void            *ifa_data;    /* Address-specific data */
 };
ifa_next字段包含一个指向列表中下一个结构的指针；如果这是列表的最后一项，则为NULL。 ifa_name指向以空值终止的接口名称。 ifa_flags字段包含接口标志，由SIOCGIFFLAGS ioctl（2）操作返回（有关这些标志的列表，请参见netdevice（7））。 ifa_addr字段指向包含接口地址的结构。 （应咨询sa_family子字段以确定地址结构的格式。）此字段可能包含空指针。 ifa_netmask字段指向包含与ifa_addr关联的网络掩码的结构（如果适用于地址族）。该字段可以包含空指针。根据是否在ifa_flags中设置了IFF_BROADCAST或IFF_POINTOPOINT位（一次只能设置一个），ifa_broadaddr将包含与ifa_addr关联的广播地址（如果适用于地址族）或ifa_dstaddr将包含与ifa_addr相关的广播地址。点对点接口。 ifa_data字段指向一个包含地址族特定数据的缓冲区。如果此接口没有此类数据，则此字段可以为NULL。由getifaddrs（）返回的数据是动态分配的，应在不再需要时使用freeifaddrs（）释放。
*/
int getifaddrs(struct ifaddrs **ifap);
void freeifaddrs(struct ifaddrs *ifa);
```

```C++
// man 3
#include <arpa/inet.h>
const char *inet_ntop(int af, const void *src, char *dst, socklen_t size);
```

### 域名解析

