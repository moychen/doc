# Linux常用API

## 文件相关

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

