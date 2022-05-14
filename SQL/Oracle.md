# Oracle手册

## 1. 常用操作

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

### 查看版本、sid、服务名

```sql
-- Oracle的服务名(ServiceName)查询
show parameter service_name;

-- Oracle的SID查询命令：
select instance_name from v$instance;

-- 查看Oracle版本
select version from v$instance;

--1、查看表空间的名称及大小
SELECT t.tablespace_name, round(SUM(bytes / (1024 * 1024)), 0) ts_size
FROM dba_tablespaces t, dba_data_files d
WHERE t.tablespace_name = d.tablespace_name
GROUP BY t.tablespace_name;
--2、查看表空间物理文件的名称及大小
SELECT tablespace_name,
file_id,
file_name,
round(bytes / (1024 * 1024), 0) total_space
FROM dba_data_files
ORDER BY tablespace_name;
--3、查看回滚段名称及大小
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
--4、查看控制文件
SELECT NAME FROM v$controlfile;
--5、查看日志文件
SELECT MEMBER FROM v$logfile;
--6、查看表空间的使用情况
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
--7、查看数据库库对象
SELECT owner, object_type, status, COUNT(*) count#
FROM all_objects
GROUP BY owner, object_type, status;
--8、查看数据库的版本　
SELECT version
FROM product_component_version
WHERE substr(product, 1, 6) = 'Oracle';
--9、查看数据库的创建日期和归档方式
SELECT created, log_mode, log_mode FROM v$database;
```

