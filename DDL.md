## DDL ：

__DDL 的英文全称是 Data Definition Language，中文是数据定义语言。它定义了数据库的结构和数据表的结构。__

在 DDL 中，我们常用的功能是增删改，分别对应的命令是 CREATE、DROP 和 ALTER。需要注意的是，在执行 DDL 的时候，不需要 COMMIT，就可以完成执行任务。


## DDL的基础语法：

### 1、CREATE相关的常用命令：

        CREATE DATABASE # 创建数据库
        CREATE EVENT
        CREATE FUNCTION
        CREATE FUNCTION UDF
        CREATE INDEX    # 创建索引
        CREATE PROCEDURE
        CREATE SERVER
        CREATE TABLE    # 创建表
        CREATE TABLESPACE
        CREATE TRIGGER
        CREATE USER     # 创建用户
        CREATE VIEW     # 创建视图

### 一、创建表：CREATE TABLE

__(1) 直接创建；如：__

    CREATE TABLE player  (
     player_id int(11) NOT NULL AUTO_INCREMENT,
     player_name varchar(255) NOT NULL
    );

__(2) 通过查询现存的表创建；新表会被直接插入查询而来的数据；如：__
   
    #创建table1表，同时将score1中查询到的数据插入到表中
    CREATE TABLE table1 AS SELECT * FROM score1;

__(3) 通过复制现存的表的表结构创建；不复制数据；__

    CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }

    如：

    #创建table2，表结构采用table1的
    CREATE TABLE table2 LIKE table1;

### 二、创建VIEW

__什么是视图：__

*   视图其实就是一条查询sql语句，用于显示一个或多个表或其他视图中的相关数据。

    视图将一个查询的结果作为一个表来使用，因此视图可以被看作是存储的查询或一个虚拟表，与真实表不同，视图不会要求分配存储空间，视图中也不会包含实际的数据。视图只是定义了一个查询，视图中的数据是从基表中获取，这些数据在视图被引用时动态的生成。由于视图基于数据库中的其他对象，因此一个视图只需要占用数据字典中保存其定义的空间，而无需额外的存储空间，并且基表的变化会导致视图相应的改变。

__创建视图的方法：__
    
    CREATE
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]

__如：__

    create view v_F_players(编号,名字,性别,电话)
    -> as
    -> select PLAYERNO,NAME,SEX,PHONENO from PLAYERS
    -> where SEX='F'
    -> with check option;
    
__效果展示：__

    mysql> desc v_F_players;
    +--------+----------+------+-----+---------+-------+
    | Field  | Type     | Null | Key | Default | Extra |
    +--------+----------+------+-----+---------+-------+
    | 编号    | int(11)  | NO   |     | NULL    |       |
    | 名字    | char(15) | NO   |     | NULL    |       |
    | 性别    | char(1)  | NO   |     | NULL    |       |
    | 电话    | char(13) | YES  |     | NULL    |       |
    +--------+----------+------+-----+---------+-------+

### 三、修改表：ALTER TABLE

__(1) 修改表结构案例：__
    
    # 在数据表中添加一个age字段 类型为int(11)
    ALTER TABLE player ADD (age int(11));

__(2) 修改字段名称：__

    # 将 age 字段改成 player_age
    ALTER TABLE player RENAME COLUMN age to player_age

__(3) 修改字段的数据类型：__

    # 将 player_age的数据类型改为 float(3,1)
    ALTER TABLE player MODIFY (player_age float(3,1));


__(4) 删除字段__

    # 删除 player_age 字段
    ALTER TABLE student DROP COLUMN player_age;

__(4) 修改索引__

    # 创建一个普通索引 product_comment 是表名 comment_text 是字段名称

    ALTER TABLE product_comment ADD INDEX (comment_text);




### 四、DROP 相关的常用命令：

__(1) 删除表__

    DROP TABLE table_name;

__(2) 删除索引__  

    DROP INDEX index_name;

__(3) 删除视图__

    DROP VIEW [IF EXISTS] view_name [, view_name] ... [RESTRICT | CASCADE]

### 五、查看表结构：

    DESCRIBE tbl_name;

### 六、SHOW 相关的常用命令：

__查看表状态信息__

    SHOW [FULL] TABLES [{FROM | IN} db_name] [LIKE 'pattern' | WHERE expr] 

    最简单的用法：SHOW TABLES;

__查看表上的索引__

    SHOW {INDEX | INDEXES | KEYS} {FROM | IN} tbl_name [{FROM | IN} db_name] [WHERE expr]   

    如：SHOW INDEX FROM score1 FROM data_analysis; 查看data_analysis库中的score1表，也可以写成 SHOW INDEX FROM data_analysis.score1


### 七、数据表的常见约束：

__主键约束：__ 

*   主键起唯一标识一条记录的作用，不能重复，不能为空，即 UNIQUE+NOT NULL。一个数据表的主键只能有一个。主键可以是一个字段，也可以由多个字段复合组成。命令创建主键的命令为 如PRIMARY KEY (`player_id`) USING BTREE 意为将player_id设置为主键，索引采用BTREE索引,在Navicat中可以在索引标题栏中写明。

__外键约束：__ 

*   外键确保了表与表之间引用的完整性。一个表中的外键对应另一张表的主键。外键可以是重复的，也可以为空。

__唯一性约束：__ 

*   唯一性约束表明了字段在表中的数值是唯一的，即使我们已经有了主键，还可以对其他字段进行唯一性约束。比如我们创建 player 表并给 player_name 设置唯一性约束，就表明任何两个球员的姓名不能相同。需要注意的是，唯一性约束和普通索引（NORMAL  INDEX）之间是有区别的。__唯一性约束相当于创建了一个约束和普通索引，目的是保证字段的正确性，而普通索引只是提升数据检索的速度，并不对字段的唯一性进行约束。__

__NOT NULL约束：__ 

*   对字段定义了 NOT NULL，即表明该字段不应为空，必须有取值。

__DEFAULT约束：__ 

*   DEFAULT，表明了字段的默认值。如果在插入数据的时候，这个字段没有取值，就设置为默认值。比如我们将身高 height 字段的取值默认设置为 0.00，即DEFAULT 0.00

__CHECK约束：__ 

*   CHECK 约束，用来检查特定字段取值范围的有效性，CHECK 约束的结果不能为 FALSE，比如我们可以对身高 height 的数值进行 CHECK 约束，必须≥0，且＜3，即CHECK(height>=0 AND height<3)


### 八、设计数据表的原则 "三少一多"

__1、数据表的个数越少越好：__

*   RDBMS 的核心在于对实体和联系的定义，也就是 E-R 图（Entity Relationship Diagram），数据表越少，证明实体和联系设计得越简洁，既方便理解又方便操作。

__2、数据表中的字段个数越少越好__

*   字段个数越多，数据冗余的可能性越大。设置字段个数少的前提是各个字段相互独立，而不是某个字段的取值可以由其他字段计算出来。当然字段个数少是相对的，我们通常会在数据冗余和检索效率中进行平衡。

__3、数据表中联合主键的字段个数越少越好__

*   设置主键是为了确定唯一性，当一个字段无法确定唯一性的时候，就需要采用联合主键的方式（也就是用多个字段来定义一个主键）。联合主键中的字段越多，占用的索引空间越大，不仅会加大理解难度，还会增加运行时间和索引空间，因此联合主键的字段个数越少越好。


__4、使用主键和外键越多越好__

*   数据库的设计实际上就是定义各种表，以及各种字段之间的关系。这些关系越多，证明这些实体之间的冗余度越低，利用度越高。这样做的好处在于不仅保证了数据表之间的独立性，还能提升相互之间的关联使用率。


### 九、外键真的越多越好吗？：

*   首先，外键本身是为了实现强一致性，所以如果需要正确性>性能的话，还是建议使用外键，它可以让我们在数据库的层面保证数据的完整性和一致性。
当然不用外键，你也可以在业务层进行实现。不过，这样做也同样存在一定的风险，因为这样，就会让业务逻辑会与数据具备一定的耦合性。也就是业务逻辑和数据必须同时修改。而且在工作中，业务层可能会经常发生变化。

    当然，很多互联网的公司，尤其是超大型的数据应用场景，大量的插入，更新和删除在外键的约束下会降低性能，同时数据库在水平拆分和分库的情况下，数据库端也做不到执行外键约束。另外，在高并发的情况下，外键的存在也会造成额外的开销。因为每次更新数据，都需要检查另外一张表的数据，也容易造成死锁。
    
    所以在这种情况下，尤其是大型项目中后期，可以采用业务层来实现，取消外键提高效率。
    
    不过在SQL学习之初，包括在系统最初设计的时候，还是建议你采用规范的数据库设计，也就是采用外键来对数据表进行约束。因为这样可以建立一个强一致性，可靠性高的数据库结构，也不需要在业务层来实现过多的检查。

    当然在项目后期，业务量增大的情况下，你需要更多考虑到数据库性能问题，可以取消外键的约束，转移到业务层来实现。而且在大型互联网项目中，考虑到分库分表的情况，也会降低外键的使用。
    不过在SQL学习，以及项目早期，还是建议你使用外键。在项目后期，你可以分析有哪些外键造成了过多的性能消耗。一般遵循2/8原则，会有20%的外键造成80%的资源效率，你可以只把这20%的外键进行开放，采用业务层逻辑来进行实现，当然你需要保证业务层的实现没有错误。不同阶段，考虑的问题不同。当用户和业务量增大的时候，对于大型互联网应用，也会通过减少外键的使用，来减低死锁发生的概率，提高并发处理能力。



