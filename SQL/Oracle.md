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

