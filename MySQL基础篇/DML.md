# DML Data Manipulation Language(数据操作语言)

__我们 通过DML可以操作数据库相关的记录，比如增加、删除、修改数据表中的记录。__

-《[个别例子中需要事先准备好表](https://github.com/OneStepAndTwoSteps/SQL-Notes/tree/master/%E6%95%B0%E6%8D%AE%E9%9B%86sql/sql_nba_data-master/)》


## DML常用语法：INSERT, DELETE, UPDATE, SELECT

### INSERT：一次插入一行或多行数据；

    # INSERT：Syntax:

    INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
        [INTO] tbl_name [(col_name,...)]
        {VALUES | VALUE} ({expr | DEFAULT},...),(...),...
        [ ON DUPLICATE KEY UPDATE
            col_name=expr
            [, col_name=expr] ... ]

    INSERT tbl_name [(col1,...)] VALUES (val1,...), (val21,...)

__Or:__

    INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
        [INTO] tbl_name
        SET col_name={expr | DEFAULT}, ...
        [ ON DUPLICATE KEY UPDATE
            col_name=expr
            [, col_name=expr] ... ]

__Or:__

    INSERT [LOW_PRIORITY | HIGH_PRIORITY] [IGNORE]
        [INTO] tbl_name [(col_name,...)]
        SELECT ...
        [ ON DUPLICATE KEY UPDATE
            col_name=expr
            [, col_name=expr] ... ]


### DELETE:
    
    #DELETE 语法：[]表示可选 {}表示必选

    DELETE [LOW_PRIORITY] [QUICK] [IGNORE] FROM tbl_name
        [WHERE where_condition]
        [ORDER BY ...]
        [LIMIT row_count]

    注意：一定要有限制条件，否则将清空表中的所有数据；
        限制条件：
            WHERE
            LIMIT
    例子：
        
        DELETE FROM  `table_name` WHERE column1 = 370;

### UPDATE：

    # UPDATE 语法：[]表示可选 {}表示必选

    UPDATE [LOW_PRIORITY] [IGNORE] table_reference
        SET col_name1={expr1|DEFAULT} [, col_name2={expr2|DEFAULT}] ...
        [WHERE where_condition]
        [ORDER BY ...]
        [LIMIT row_count]

    注意：一定要有限制条件，否则将修改所有行的指定字段；
        限制条件：
            WHERE
            LIMIT

    例子：

        UPDATE table_name SET column_name=values WHERE column_name=values

        # 更新player表中的height=2.12 条件是player_id=10003
        UPDATE player SET height=2.12 WHERE player_id=10003

## SELECT: 

Mysql8.0以前：查询执行路径中的组件：查询缓存、解析器、预处理器、优化器、查询执行引擎、存储引擎；

__SELECT语句 关键字的顺序：__

    SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ...

__SELECT语句的执行流程：__

    FROM Clause --> WHERE Clause --> GROUP BY --> HAVING Clause --> ORDER BY --> SELECT --> LIMIT

    SELECT [DISTINCT] 查询列表
    [FROM 表名]
    [WHERE 布尔表达式]
    [GROUP BY 分组列表 ]
    [HAVING 分组过滤条件]
    [ORDER BY 排序列表]
    [LIMIT 开始行, 限制条数]

__比如一个SQL语句的查询顺序：__

    SELECT DISTINCT player_id, player_name, count(*) as num # 顺序 5
    FROM player JOIN team ON player.team_id = team.team_id # 顺序 1
    WHERE height > 1.80 # 顺序 2
    GROUP BY player.team_id # 顺序 3
    HAVING num > 2 # 顺序 4
    ORDER BY num DESC # 顺序 6
    LIMIT 2 # 顺序 7

## SHOW:

查看表的一些信息

show table status like "表名(支持模糊匹配)"\G


    MariaDB [test]> show table status like "t"\G
    *************************** 1. row ***************************
            Name: t
            Engine: InnoDB
            Version: 10
        Row_format: Compact         # 行记录格式
            Rows: 0
    Avg_row_length: 0
        Data_length: 16384
    Max_data_length: 0
    Index_length: 0
        Data_free: 5242880
    Auto_increment: NULL
        Create_time: 2020-03-14 15:44:53
        Update_time: NULL
        Check_time: NULL
        Collation: utf8_general_ci  # 字符规范
        Checksum: NULL
    Create_options: 
            Comment: 
    1 row in set (0.00 sec)




## 单表查询：

    # SELECT 语法：[]表示可选 {}表示必选__

    SELECT
        [ALL | DISTINCT | DISTINCTROW ]
            [SQL_CACHE | SQL_NO_CACHE] 
        select_expr [, select_expr ...]
        [FROM table_references
        [WHERE where_condition]
        [GROUP BY {col_name | expr | position}
            [ASC | DESC], ... [WITH ROLLUP]]
        [HAVING where_condition]
        [ORDER BY {col_name | expr | position}
            [ASC | DESC], ...]
        [LIMIT {[offset,] row_count | row_count OFFSET offset}]
        [FOR UPDATE | LOCK IN SHARE MODE]

## SELECT 常规用法：

    SELECT 字段1,字段2... FROM 表1
    SELECT * FROM 表1

    # 将查询到的字典进行别名命名，然后显示出来
    SELECT 字段1 AS 别名1, 字段2 AS 别名2, 字段3 AS 别名3 FROM 表1

    # 增加一列字段名为platform的字段，内容定义为 查询常数1，同时展示字段1，从表1中
    SELECT '查询常数1' as platform, 字段1 FROM 表1
    
    显示格式：如下

    platform        字段1
    查询常数1       内容1
    查询常数1       内容2
    查询常数1       内容3
    查询常数1       内容4

#### DISTINCT: 数据去重；

__注意：__

1、DISTINCT 需要放到所有列名的前面，如果写成 SELECT 字段1, DISTINCT 字段2 FROM 表1 会报错

2、DISTINCT 后面跟几个字段代表这几个字段结合之后的内容去重，例如 SELECT DISTINCT name,age FROM table1 会将name+age组合之后的内容去重

    # 以字段1为去重对象进行去重
    SELECT DISTINCT 字段1 FROM 表1


#### 显式指明是否使用缓存查询内容:

    SQL_CACHE: 显式指定存储查询结果于缓存之中；

    SQL_NO_CACHE: 显式查询结果不予缓存；

    query_cache_type的值为'ON'时，查询缓存功能打开；
        SELECT的结果符合缓存条件即会缓存，否则，不予缓存；
        显式指定SQL_NO_CACHE，不予缓存；

    query_cache_type的值为'DEMAND'时，查询缓存功能按需进行；
        显式指定SQL_CACHE的SELECT语句才会缓存；其它均不予缓存；

__例子：__  

    SELECT SQL_CACHE  *  FROM table1;

### WHERE子句：指明过滤条件以实现“选择”的功能：

    过滤条件：布尔型表达式；

    算术操作符：+, -, *, /, %
    比较操作符：=, !=, <>, <=>, >, >=, <, <=

        BETWEEN min_num AND max_num
        IN (element1, element2, ...)
        IS NULL
        IS NOT NULL
    
        LIKE: 
            通配符：% 代表任意长度的任意字符；
            通配符：_ 代表任意单个字符；

        RLIKE：
        REGEXP：匹配字符串可用正则表达式书写模式；

    逻辑操作符：

        NOT
        AND
        OR
        XOR

__使用通配符时注意：__ 不过在实际操作过程中，我们还是建议尽量少用通配符，因为它需要消耗数据库更长的时间来进行匹配。即使对 LIKE 检索的字段进行了索引，索引的价值也可能会失效。如果要让索引生效，那么 LIKE 后面就不能以（%）开头，比如使用LIKE '%太%'或者LIKE '%太'的时候就会对全表进行扫描。如果使用LIKE '太%'，同时检索的字段进行了索引的时候，则不会进行全表扫描。

### GROUP BY：根据指定的条件把查询结果进行“分组”以用于做“聚合”运算：


注意事项：

*   如果分组列中含有 `NULL` 值，那么 `NULL` 也会作为一个独立的分组存在。

*   如果存在多个分组列，也就是嵌套分组，聚集函数将作用在最后的那个分组列上，如：

        # 分组之后再分组
        SELECT department, major, COUNT(*) FROM student_info GROUP BY department, major;

        最后的聚集函数作用在 major 上，major有多少个计数，最后就显示多少。

*   如果查询语句中存在 `WHERE` 子句和 `ORDER BY` 子句，那么 `GROUP BY` 子句必须出现在 `WHERE` 子句之后，`ORDER BY` 子句之前。

*   `非分组列` 不能单独出现在检索列表中(可以被放到聚集函数中),如果设置了 `ONLY_FULL_GROUP_BY` 模式，可以放置非分组类，如：

        1、规范：SELECT  subject, AVG(score) FROM student_score GROUP BY subject;

        规范的写法就是 GROUP BY subject 那么前面的 SEKECT 的内容就只写 subject，或者再带上聚集函数。

        2、不规范：SELECT number, subject, AVG(score) FROM student_score GROUP BY subject;

        在 SELECT 的时候引入了非分组类：number，所以不规范，即使开了 ONLY_FULL_GROUP_BY 模式，最后的出来的 number 列也没有什么实际意义，如，显示的 number 列数据内容全一致。

*   `GROUP BY` 子句后也可以跟随表达式(但不能是聚集函数)。

常用聚集函数：

    avg(), max(), min(), count(), sum()

可以针对分组的条件放到 HAVING 子句中 (HAVING: 对分组聚合运算后的结果指定过滤条件；),如：

    # 查询平均分大于73分的课程
    SELECT subject, AVG(score) FROM student_score GROUP BY subject HAVING AVG(score) > 73;

        

### ORDER BY: 根据指定的字段对查询结果进行排序；

__1、排序的列名：__ 

ORDER BY 后面可以有一个或多个列名，如果是多个列名进行排序，会按照后面第一个列先进行排序，当第一列的值相同的时候，再按照第二列进行排序，以此类推。

__2、排序的顺序：__

ORDER BY 后面可以注明排序规则，ASC 代表递增排序，DESC 代表递减排序。如果没有注明排序规则，默认情况下是按照 ASC 递增排序。我们很容易理解 ORDER BY 对数值类型字段的排序规则，但如果排序字段类型为文本数据，就需要参考数据库的设置方式了，这样才能判断 A 是在 B 之前，还是在 B 之后。比如使用 MySQL 在创建字段的时候设置为 BINARY 属性，就代表区分大小写。

__3、非选择列排序：__

ORDER BY 可以使用非选择列进行排序，所以即使在 SELECT 后面没有这个列名，你同样可以放到 ORDER BY 后面进行排序。 __可以依靠一个不显示出来的字段进行排序__

__4、ORDER BY 的位置：__ 

ORDER BY 通常位于 SELECT 语句的最后一条子句，否则会报错。 __可以参考上面的SELECT语句的执行流程__

    升序：ASC
    降序：DESC

__LIMIT [[offset,]row_count]：对查询的结果进行输出行数数量限制；__

    对查询结果中的数据请求施加“锁”：
        FOR UPDATE: 写锁，排他锁；
        LOCK IN SHARE MODE: 读锁，共享锁


### HAVING 和 WHERE 的区别：

当我们创建出很多分组的时候，有时候就需要对分组进行过滤。你可能首先会想到 WHERE 子句，实际上过滤分组我们使用的是 HAVING。HAVING 的作用和 WHERE 一样，都是起到过滤的作用，只不过 __WHERE 是用于数据行，而 HAVING 则作用于分组__。同时我们应该知道，__HAVING 支持所有 WHERE 的操作，因此所有需要 WHERE 子句实现的功能，你都可以使用 HAVING 对分组进行筛选。__

__案例:王者荣耀英雄属性数据__

问：

比如我们想要按照英雄的主要定位、次要定位进行分组，并且筛选分组中英雄数量大于 5 的组，最后按照分组中的英雄数量从高到低进行排序。

答：

首先我们需要获取的是英雄的数量、主要定位和次要定位，即SELECT COUNT(*) as num, role_main, role_assist。然后按照英雄的主要定位和次要定位进行分组，即GROUP BY role_main, role_assist，同时我们要对分组中的英雄数量进行筛选，选择大于 5 的分组，即HAVING num > 5，然后按照英雄数量从高到低进行排序，即ORDER BY num DESC。

    SQL: SELECT COUNT(*) as num, role_main, role_assist FROM heros GROUP BY role_main, role_assist HAVING num > 5 ORDER BY num DESC

运行结果:

                    
    num     role_main(主要定位)   role_assist(次要定位)
    12        法师
    9         射手
    3         战士
    5         战士                    坦克

__注意：__ 如果把 HAVING 替换成了 WHERE，SQL 则会报错。对于分组的筛选，我们一定要用 HAVING，而不是 WHERE。另外你需要知道的是，HAVING 支持所有 WHERE 的操作，因此所有需要 WHERE 子句实现的功能，你都可以使用 HAVING 对分组进行筛选。

### 小结

__在进行SQL查询时不同的SQL语句查询的效率相差很大，保持高效率的一个方法就是要避免全表扫描，解决方法有在WHERE语句和在ORDER BY涉及到的列中增加索引。__

问：

    我们既然在WHERE语句中加了索引，为什么在ORDER BY语句中还要加索引？   

答：

    在MySQL中，支持两种排序方式：FileSort和Index排序。Index排序的效率更高，
    Index排序：索引可以保证数据的有序性，因此不需要再进行排序。
    FileSort排序：一般在内存中进行排序，占用CPU较多。如果待排结果较大，会产生临时文件I/O到磁盘进行排序，效率较低。

    所以使用ORDER BY子句时，应该尽量使用Index排序，避免使用FileSort排序。
    当然具体优化器是否采用索引进行排序，你可以使用explain来进行执行计划的查看。

    优化建议：

    1、SQL中，可以在WHERE子句和ORDER BY子句中使用索引，目的是在WHERE子句中避免全表扫描，ORDER BY子句避免使用FileSort排序。
    当然，某些情况下全表扫描，或者FileSort排序不一定比索引慢。但总的来说，我们还是要避免，以提高查询效率。
    一般情况下，优化器会帮我们进行更好的选择，当然我们也需要建立合理的索引。

    2、尽量Using Index完成ORDER BY排序。如果WHERE和ORDER BY相同列就使用单索引列；如果不同使用联合索引。

    3、无法Using Index时，对FileSort方式进行调优。

#### 使用索引查询时注意：

    除了考虑建立字段索引以外，你还需要考虑索引是否会存在失效，所以我们应该避免以下几点：

    1、不要在WHERE子句后面对字段做函数处理，同时也避免对索引字段进行数据类型转换

    2、避免在索引字段上使用<>，!=，以及对字段进行NULL判断（包括 IS NULL, IS NOT NULL）

    3、在索引字段后，慎用IN和NOT IN，如果是连续的数值，可以考虑用BETWEEN进行替换

__因为在WHERE子句中，如果对索引字段进行了函数处理，或者使用了<>,!=或NULL判断等，都会造成索引失效。__


## 多表查询：

#### 交叉连接：笛卡尔乘积；

笛卡尔乘积是一个数学运算。假设我有两个集合 X 和 Y，那么 X 和 Y 的笛卡尔积就是 X 和 Y 的所有可能组合，也就是第一个对象来自于 X，第二个对象来自于 Y 的所有可能。

    我们假定 player 表的数据是集合 X，先进行 SQL 查询：

        SELECT * FROM player

    再假定 team 表的数据为集合 Y，同样需要进行 SQL 查询：
        
        SELECT * FROM team

    接着我们再来看下两张表的笛卡尔积的结果，这是笛卡尔积的调用方式：

        SQL: SELECT * FROM player, team


#### 内连接：

等值连接：让表之间的字段以“等值”建立连接关系；

    SQL: SELECT player_id, player.team_id, player_name, height, team_name FROM player, team 
    WHERE player.team_id = team.team_id

不等值连接

    SQL：SELECT p.player_name, p.height, h.height_level FROM player AS p, height_grades AS h
    WHERE p.height BETWEEN h.height_lowest AND h.height_highest


自连接

    SQL：SELECT b.player_name, b.height FROM player as a , player as b 
    WHERE a.player_name = '布雷克 - 格里芬' and a.height < b.height

自然连接
        
它会帮你自动查询两张连接表 __所有相同的字段__，然后进行等值连接。
    
    SELECT player_id, team_id, player_name, height, team_name FROM player NATURAL JOIN team 



#### 外连接：

左外连接：

    # +表示的表是从表，此处表示左外连接
    SQL92标准：SELECT * FROM player, team where player.team_id = team.team_id(+)  
    
    SQL99标准：SELECT * FROM player LEFT JOIN team on player.team_id = team.team_id

右外连接

    # +表示的表是从表，此处表示右外连接
    SQL92标准：SELECT * FROM player, team where player.team_id(+) = team.team_id

    SQL99标准：SELECT * FROM player RIGHT JOIN team on player.team_id = team.team_id


#### USING 连接

当我们进行连接的时候，可以用 USING __指定数据表里的同名字段进行等值连接__。比如：

    SELECT player_id, team_id, player_name, height, team_name FROM player JOIN team USING(team_id)


#### 子查询：在查询语句嵌套着查询语句

基于某语句的查询结果再次进行的查询

用在WHERE子句中的子查询：

    (1) 用于比较表达式中的子查询；子查询仅能返回单个值；
        SELECT Name,Age FROM students WHERE Age>(SELECT avg(Age) FROM students);
    (2) 用于IN中的子查询：子查询应该单键查询并返回一个或多个值从构成列表；
        SELECT Name,Age FROM students WHERE Age IN (SELECT Age FROM teachers);
    (3) 用于EXISTS；

用于FROM子句中的子查询；

    使用格式：SELECT tb_alias.col1,... FROM (SELECT clause) AS tb_alias WHERE Clause; 
    示例：
    SELECT s.aage,s.ClassID FROM (SELECT avg(Age) AS aage,ClassID FROM students WHERE ClassID IS NOT NULL GROUP BY ClassID) AS s WHERE s.aage>30;

#### 联合查询：UNION

    SELECT Name,Age FROM students UNION SELECT Name,Age FROM teachers;


#### 多表连接公式

    SELECT ...
    FROM table1
        JOIN table2 ON table1 和 table2 的连接条件
            JOIN table3 ON table_num 和 table3 的连接条件  #table_num为table1或者table2


#### 多表嵌套公式

    for t1 in table1:
        for t2 in table2:
        if condition1:
            for t3 in table3:
                if condition2:
                    output t1 + t2 + t3





#### SELECT 小结：

在 SELECT 语句执行步骤的时候，每个步骤都会产生一个虚拟表，然后将这个虚拟表传入下一个步骤中作为输入。需要注意的是，这些步骤隐含在 SQL 的执行过程中，对于我们来说是不可见的。

首先，你可以注意到，SELECT 是先执行 FROM 这一步的。在这个阶段，如果是多张表联查，还会经历下面的几个步骤：

    1、首先先通过 CROSS JOIN 求笛卡尔积，相当于得到虚拟表 vt（virtual table）1-1；

    2、通过 ON 进行筛选，在虚拟表 vt1-1 的基础上进行筛选，得到虚拟表 vt1-2；

    3、添加外部行。如果我们使用的是左连接、右链接或者全连接，就会涉及到外部行，也就是在虚拟表 vt1-2 的基础上增加外部行，得到虚拟表 vt1-3。

如果我们操作的是两张以上的表，还会重复上面的步骤，直到所有表都被处理完为止。这个过程得到是我们的原始数据。

    1、当我们拿到了查询数据表的原始数据，也就是最终的虚拟表 vt1，就可以在此基础上再进行 WHERE 阶段。在这个阶段中，会根据 vt1 表的结果进行筛选过滤，得到虚拟表 vt2。

    2、然后进入第三步和第四步，也就是 GROUP 和 HAVING 阶段。在这个阶段中，实际上是在虚拟表 vt2 的基础上进行分组和分组过滤，得到中间的虚拟表 vt3 和 vt4。

    3、当我们完成了条件筛选部分之后，就可以筛选表中提取的字段，也就是进入到 SELECT 和 DISTINCT 阶段。

    4、首先在 SELECT 阶段会提取想要的字段，然后在 DISTINCT 阶段过滤掉重复的行，分别得到中间的虚拟表 vt5-1 和 vt5-2。

    5、当我们提取了想要的字段数据之后，就可以按照指定的字段进行排序，也就是 ORDER BY 阶段，得到虚拟表 vt6。

    6、最后在 vt6 的基础上，取出指定行的记录，也就是 LIMIT 阶段，得到最终的结果，对应的是虚拟表 vt7。

    7、当然我们在写 SELECT 语句的时候，不一定存在所有的关键字，相应的阶段就会省略。



