# 3rd

## 1. libevent

### 1.1 编译安装

#### 1.1.1 windows下交叉编译

使用visual studio自带的交叉编译工具。新建build_zlib.bat编译zlib。

```bat
set VS="D:\IDE\VS2019\VC\Auxiliary\Build\vcvarsx86_amd64.bat"
set OUT=F:\Codebase\libevent\out\zlib
call %VS%

cd F:\Codebase\libevent\zlib-1.2.11
nmake /f win32\Makefile.msc clean
nmake /f win32\Makefile.msc

md %OUT%
md %OUT%\lib
md %OUT%\bin
md %OUT%\include

copy /Y *.lib %OUT%\lib
copy /Y *.h %OUT%\include
copy /Y *.exe %OUT%\bin
copy /Y *.dll %OUT%\bin
pause
```

新建build_openssl.bat编译open-ssl，需要安装perl、nasm。

**perl Configure [VC-WIN32/VC-WIN64A/VC-WIN64I} --prefix=%OUTPATH% **

```bat
set VS="D:\IDE\VS2019\VC\Auxiliary\Build\vcvarsx86_amd64.bat"
set OUT=F:\Codebase\libevent\out\openssl
call %VS%

cd F:\Codebase\libevent\openssl-1.1.1
perl Configure VC-WIN64A --prefix=%OUT%

md %OUT%

nmake clean
nmake
nmake install
@echo "编译openssl完成"
pause
```

新增build_libevent.bat编译libevent。

```bat
set VS="D:\IDE\VS2019\VC\Auxiliary\Build\vcvarsx86_amd64.bat"
set OUT=F:\Codebase\libevent\out\libvent
call %VS%

cd F:\Codebase\libevent\libevent-master

nmake /f Makefile.nmake clean
nmake /f Makefile.nmake OPENSSL_DIR=F:\Codebase\libevent\out\openssl

md %OUT%
md %OUT%\lib
md %OUT%\bin
md %OUT%\include


::/Y表示不提示 /S表示包含目录
copy /Y *.lib %OUT%\lib
xcopy /S/Y include %OUT%\include\
xcopy /S/Y WIN32-Code\nmake %OUT%\include\
copy /Y *.exe %OUT%\bin
copy /Y *.dll %OUT%\bin

@echo "编译libevent完成"
pause
```

#### 1.1.2 Linux 下编译

**zlib:**

```bash
$ ./configure
$ make && make install
```

**openssl**:

```bash
$ ./config
$ make && make install
```

**libevent**

```bash
$ cd libevent
$ ./autogen.sh
$ ./configure
$ make && make install
```

### 2. libevent 常用接口

#### 2.1 event_base

#### 2.2 bufferevent

### 3. libevent实战



## 2. boost

### 2.1 编译安装

#### 2.1.1 windows下编译

