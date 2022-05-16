# Oracle手册

## 1. 常用操作

### 查看监听

```
重启Oracle数据库的操作步骤
1.查看监听器状态：lsnrctl status
2.停止监听器：lsnrctl stop
3.连接数据库：sqlplus / as sysdba
4.停止数据库：shutdown immediate
5.启动数据库：startup
6.退出数据库：exit
7.启动监听：lsnrctl start
```

```bash
$ ps -ef | grep lsn
$ lsnrctl status
```

### 登录

```
sqlplus [ [<option>] [{logon | /nolog}] [<start>] ]
<option> 为: [-C <version>] [-L] [-M "<options>"] [-R <level>] [-S]

-C <version>   将受影响的命令的兼容性设置为<version> 指定的版本。该版本具有"x.y[.z]" 格式。例如, -C 10.2.0
-L             只尝试登录一次, 而不是 在出错时再次提示。
-M "<options>" 设置输出的自动 HTML 标记。选项的格式为:
               HTML [ON|OFF] [HEAD text] [BODY text] [TABLE text][ENTMAP {ON|OFF}] [SPOOL {ON|OFF}] [PRE[FORMAT] {ON|OFF}]
-R <level>     设置受限(restricted)模式, 以禁用与文件系统交互的SQL*Plus 命令。级别可以是 1, 2 或 3。最高限制级别为 -R 3, 该级别禁用与文件系统交互的所有用户命令。
-S             设置无提示(slient)模式, 该模式隐藏命令的提示和回显 的显示。

 <logon> 为: {<username>[/<password>][@<connect_identifier>] | / }[AS {SYSDBA | SYSOPER | SYSASM}] [EDITION=value]

 指定数据库帐户用户名, 口令和数据库连接的连接标识符。如果没有连接标识符, SQL*Plus 将连接到默认数据库。
 AS SYSDBA, AS SYSOPER 和 AS SYSASM 选项是数据库管理权限。

 <connect_identifier> 的形式可以是 Net 服务名或轻松连接。
   @[<net_service_name> | [//]Host[:Port]/<service_name>]
   <net_service_name> 是服务的简单名称, 它解析为连接描述符。
   示例: 使用 Net 服务名连接到数据库, 且数据库 Net 服务名为 ORCL。
      sqlplus myusername/mypassword@ORCL
   Host 指定数据库服务器计算机的主机名或 IP地址。
   Port 指定数据库服务器上的监听端口。
   <service_name> 指定要访问的数据库的服务名。
   示例: 使用轻松连接连接到数据库, 且服务名为 ORCL。
      sqlplus myusername/mypassword@Host/ORCL

 /NOLOG 选项可启动 SQL*Plus 而不连接到数据库。
 EDITION 指定会话版本的值。

<start> 为: @<URL>|<filename>[.<ext>] [<parameter> ...]
使用将分配给脚本中的替代变量的指定参数从 Web 服务器 (URL) 或本地文件系统 (filename.ext)运行指定的 SQL*Plus 脚本。

在启动 SQL*Plus 并且执行 CONNECT 命令后, 将运行站点概要文件 (例如, $ORACLE_HOME/sqlplus/admin/glogin.sql) 和用户概要文件例如, 工作目录中的 login.sql)。这些文件包含 SQL*Plus 命令。
```

```bash
$ sqlplus username/pwd@host/service_name
$ sqlplus "/as sysdba"
```

### 数据库操作

### 常用函数

```plsql
decode(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值)
该函数的含义如下：
    IF 条件=值1 THEN
        RETURN(翻译值1)
    ELSIF 条件=值2 THEN
        RETURN(翻译值2)
        ......
    ELSIF 条件=值n THEN
        RETURN(翻译值n)
    ELSE
        RETURN(缺省值)
    END IF
decode(字段或字段的运算，值1，值2，值3）
	这个函数运行的结果是，当字段或字段的运算的值等于值1时，该函数返回值2，否则返回值3
当然值1，值2，值3也可以是表达式.
       
NVL(eExpression1, eExpression2)
如果 eExpression1 的计算结果为 null 值，则 NVL( ) 返回 eExpression2。如果 eExpression1 的计算结果不是 null 值，则返回 eExpression1。eExpression1 和 eExpression2 可以是任意一种数据类型。如果 eExpression1 与 eExpression2 的结果皆为 null 值，则 NVL( ) 返回 .NULL.。
返回值可以是字符型、日期型、日期时间型、数值型、货币型、逻辑型或 null 值。

to_date('2020-5-01 00:00:00', 'yyyy-mm-dd hh24:mi:ss');
TO_NUMBER(TO_CHAR(VALID_DATE, 'YYYYMMDDHH24MISS'));
```

### 管理语句

```sql
-- 查看被锁的表 
select b.owner,b.object_name,a.session_id,a.locked_mode from v$locked_object a,dba_objects b where b.object_id = a.object_id;

-- 查看那个用户那个进程照成死锁
select b.username,b.sid,b.serial#,logon_time from v$locked_object a,v$session b where a.session_id = b.sid order by b.logon_time;

-- 查看连接的进程 
SELECT sid, serial#, username, osuser FROM v$session;

-- 3.查出锁定表的sid, serial#,os_user_name, machine_name, terminal，锁的type,mode
SELECT s.sid, s.serial#, s.username, s.schemaname, s.osuser, s.process, s.machine,
s.terminal, s.logon_time, l.type
FROM v$session s, v$lock l
WHERE s.sid = l.sid
AND s.username IS NOT NULL
ORDER BY sid;
/*这个语句将查找到数据库中所有的DML语句产生的锁，还可以发现，
任何DML语句其实产生了两个锁，一个是表锁，一个是行锁。*/

-- 杀掉进程 sid,serial#
alter system kill session'210,11562';
```

### 查看版本、sid、服务名

```sql
-- Oracle的服务名(ServiceName)查询
show parameter service_name;

-- Oracle的SID查询命令：
select instance_name from v$instance;

-- 查看Oracle版本
select version from v$instance;

-- 1、查看表空间的名称及大小
SELECT t.tablespace_name, round(SUM(bytes / (1024 * 1024)), 0) ts_size
FROM dba_tablespaces t, dba_data_files d
WHERE t.tablespace_name = d.tablespace_name
GROUP BY t.tablespace_name;
-- 2、查看表空间物理文件的名称及大小
SELECT tablespace_name,
file_id,
file_name,
round(bytes / (1024 * 1024), 0) total_space
FROM dba_data_files
ORDER BY tablespace_name;
-- 3、查看回滚段名称及大小
SELECT segment_name,
tablespace_name,
r.status,
(initial_extent / 1024) initialextent,
(next_extent / 1024) nextextent,
max_extents,
v.curext curextent
FROM dba_rollback_segs r, v$rollstat v
WHERE r.segment_id = v.usn(+)
ORDER BY segment_name;
-- 4、查看控制文件
SELECT NAME FROM v$controlfile;
-- 5、查看日志文件
SELECT MEMBER FROM v$logfile;
-- 6、查看表空间的使用情况
SELECT SUM(bytes) / (1024 * 1024) AS free_space, tablespace_name
FROM dba_free_space
GROUP BY tablespace_name;
SELECT a.tablespace_name,
a.bytes total,
b.bytes used,
c.bytes free,
(b.bytes * 100) / a.bytes "% USED ",
(c.bytes * 100) / a.bytes "% FREE "
FROM sys.sm$ts_avail a, sys.sm$ts_used b, sys.sm$ts_free c
WHERE a.tablespace_name = b.tablespace_name
AND a.tablespace_name = c.tablespace_name;
-- 7、查看数据库库对象
SELECT owner, object_type, status, COUNT(*) count#
FROM all_objects
GROUP BY owner, object_type, status;
-- 8、查看数据库的版本　
SELECT version
FROM product_component_version
WHERE substr(product, 1, 6) = 'Oracle';
-- 9、查看数据库的创建日期和归档方式
SELECT created, log_mode, log_mode FROM v$database;
```

### 表空间

```sql
-- 查看默认表空间
select username, default_tablespace, temporary_tablespace from dba_users;

-- 修改默认表空间
alter user user default tablespace tablespaceName;
```

**修改表空间大小**

```sql
-- 如果系统开启审计功能，可以将通过以下命令将审计日志表清空。（此步骤只针对 SYSTEM 表空间，其他表空间请跳过）
TRUNCATE TABLE SYS.AUD$;

-- 查询需要缩小表空间对应的数据文件的 FILE_ID。
SELECT FILE_ID, FILE_NAME FROM DBA_DATA_FILES WHERE TABLESPACE_NAME = 'SYSTEM';

-- 查询该数据文件中数据所在的数据块的最大位置
SELECT MAX(BLOCK_ID)*8/1024 FROM DBA_EXTENTS WHERE FILE_ID=1;

-- 修改该数据文件的尺寸。根据需要调整数据文件的大小，但是不能低于上一步骤的查询值。
alter database datafile '文件名' resize 100m;

-- 扩展表空间
alter tablespace XGDATA add datafile '/sw02/oracle/app/oracledata/njwqjfcs/XGDATA08.dbf' size 10240M;
alter database datafile '文件名' resize 100m;
```

### 数据导入导出

#### sqlldr

### 备份与还原

#### exp/imp

```
Oracle EXP
EXP的所有参数（括号中为参数的默认值）：
USERID 用户名/口令 如： USERID=duanl/duanl 
FULL 导出整个数据库 (N)
BUFFER 数据缓冲区的大小 
OWNER 所有者用户名列表,你希望导出哪个用户的对象，就用owner=username
FILE 输出文件 (EXPDAT.DMP) 
TABLES 表名列表 ,指定导出的table名称，如：TABLES=table1,table2
COMPRESS 导入一个extent (Y) 
RECORDLENGTH IO 记录的长度
GRANTS 导出权限 (Y) 
INCTYPE 增量导出类型
INDEXES 导出索引 (Y) 
RECORD 跟踪增量导出 (Y)
ROWS 导出数据行 (Y) 
PARFILE 参数文件名,如果你exp的参数很多，可以存成参数文件.
CONSTRAINTS 导出约束 (Y) 
CONSISTENT 交叉表一致性
LOG 屏幕输出的日志文件 
STATISTICS 分析对象 (ESTIMATE)
DIRECT 直接路径 (N) 
TRIGGERS 导出触发器 (Y)
FEEDBACK 显示每 x 行 (0) 的进度
FILESIZE 各转储文件的最大尺寸
QUERY 选定导出表子集的子句
下列关键字仅用于可传输的表空间
TRANSPORT_TABLESPACE 导出可传输的表空间元数据 (N)
TABLESPACES 将传输的表空间列表
```

```
Oracle IMP参数列表：

USERID 用户名/口令 

FULL 导入整个文件 (N)
BUFFER 数据缓冲区大小 
FROMUSER 所有人用户名列表
FILE 输入文件 (EXPDAT.DMP) 
TOUSER 用户名列表
SHOW 只列出文件内容 (N)
TABLES 表名列表
IGNORE 忽略创建错误 (N) 
RECORDLENGTH IO 记录的长度
GRANTS 导入权限 (Y) 
INCTYPE 增量导入类型
INDEXES 导入索引 (Y) 
COMMIT 提交数组插入 (N)
ROWS 导入数据行 (Y) 
PARFILE 参数文件名
LOG 屏幕输出的日志文件 
CONSTRAINTS 导入限制 (Y)
DESTROY 覆盖表空间数据文件 (N)
INDEXFILE 将表/索引信息写入指定的文件
SKIP_UNUSABLE_INDEXES 跳过不可用索引的维护 (N)
ANALYZE 执行转储文件中的 ANALYZE 语句 (Y)
FEEDBACK 显示每 x 行 (0) 的进度
TOID_NOVALIDATE 跳过指定类型 id 的校验
FILESIZE 各转储文件的最大尺寸
RECALCULATE_STATISTICS 重新计算统计值 (N)
下列关键字仅用于可传输的表空间
TRANSPORT_TABLESPACE 导入可传输的表空间元数据 (N)
TABLESPACES 将要传输到数据库的表空间
DATAFILES 将要传输到数据库的数据文件
TTS_OWNERS 拥有可传输表空间集中数据的用户
```



#### 数据泵 expdp/impdp

​		所有的Data Pump导出和导入处理，包括转储文件的读写，都是在指定的数据库连接字符串所选择的服务器（即oracle所在服务器）上进行。这意味着对于非特权用户，数据库管理员（DBA）必须为在该服务器文件系统上读取和写入的Data Pump文件创建目录对象。(出于安全考虑，DBA必须确保只有经过批准的用户才可以访问目录对象)。对于有特权的用户，有一个默认的目录对象可用。

​		数据泵不加载具有禁用唯一索引的表。要将数据加载到表中，索引必须被放弃或重新启用。

