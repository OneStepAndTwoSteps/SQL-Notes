## SQL函数：

__SQL的内置函数一般分为四类__

*   1、算数函数

*   2、字符串函数

*   3、日期函数

*   4、转换函数


### 算数函数

*   ABS() 取绝对值 
    
    如 SELECT ABS(-2) 运行结果为2

*   MOD() 取余 

    如 SELECT MOD(101,3) 运行结果为 2

*   ROUND() 四舍五入为指定的小数位数，需要有两个参数，分别为字段名称、小数位数

    如 SELECT ROUND(37.25,1) 运行结果为 37.3

### 字符串函数

常用的字符串函数操作包括了字符串拼接，大小写转换，求长度以及字符串替换和截取等。

*   COUCAT() 将多个字符串进行拼接
    
        SELECT CONCAT('ABC', abc)，运行结果为 ABCabc。

*   LENGTH() 计算字段的长度，一个数字或者字母算一个字符，一个汉字算三个字符
 
        SELECT LENGTH('你好a')，运行结果为 7。

*   CHAR_LENGTH() 计算字段的长度，汉字、数字、字母都算一个字符

        SELECT CHAR_LENGTH('你好a')，运行结果为 3。

*   LOWER() 将字符串转为小写

        SELECT LOWER('ABC')，运行结果为abc 。

*   UPPER() 将字符串转为大写

        SELECT UPPER('abc')，运行结果为ABC 。

*   REPLACE() 替换函数，有三个参数，分别为：要替换的表达式或字段名，想要查找的被替换的字符串，替换成哪个字符串

        SELECT REPLACE('fabcd', 'abc', 123)，运行结果为 f123d。

*   SUBSTRING() 截取字符串，有三个参数，分为：待截取的表达式或字段名、开始截取的位置、想要截取的字符串长度

        SELECT SUBSTRING('fabcd', 1,3)，运行结果为 fab。


### 日期函数

*   CURRENT_DATA() 当前系统时间

        SELECT CURRENT_DATE()，运行结果为 2019-04-03。

*   CURRENT_TIME() 系统当前时间，没有具体的日期

        SELECT CURRENT_TIME()，运行结果为 21:26:34。

*   CURRENT_TIMESTAMP() 系统当前时间，包括具体的日期+时间

        SELECT CURRENT_TIMESTAMP()，运行结果为 2019-04-03 21:26:34。

*   EXTRACT() 抽取具体的年、月、日

        SELECT EXTRACT(YEAR FROM '2019-04-03')，运行结果为 2019。

*   DATE() 返回时间的日期部分 

        SELECT DATE('2019-04-01 12:00:05')，运行结果为 2019-04-01。

*   YEAR() 返回时间的年份部分 

        SELECT YEAR('2019-04-01 12:00:05')，运行结果为 2019。

*   MONTH() 返回日期的月份部分

        SELECT MONTH('2019-04-01 12:00:05')，运行结果为 4。

*   DAY() 返回时间的天数部分

        SELECT DAY('2019-04-01 12:00:05')，运行结果为 1 。

*   HOUR() 返回时间的小时部分

        SELECT HOUR('2019-04-01 12:00:05')，运行结果为 12 。

*   MINUTE() 返回时间的分钟部分

        SELECT MINUTE('2019-04-01 12:00:05')，运行结果为 0 。

*   SECOND() 返回时间的秒部分

        SELECT SECOND('2019-04-01 12:00:05')，运行结果为 5 。



### 转换函数

*   CAST() 数据类型转换，参数是一个表达式，表达式通过AS关键词分割了2个参数，分别是原始数据和目标数据类型

        SELECT CAST(123.123 AS INT)，运行结果会报错。

        SELECT CAST(123.123 AS DECIMAL(8,2))，运行结果为 123.12。

*   COALESCE() 返回第一个非空数值

        SELECT COALESCE(null,1,2)，运行结果为 1。

CAST 函数在转换数据类型的时候，不会四舍五入，如果原数值有小数，那么转换为整数类型的时候就会报错。不过你可以指定转化的小数类型，在 MySQL 和 SQL Server 中，你可以用来指定，其中 a 代表整数部分和小数部分加起来最大的位数，b 代表小数位数，比如代表的是精度为 8 位（整数加小数位数最多为 8 位），小数位数为 2 位的数据类型。所以的转换结果为 123.12。








