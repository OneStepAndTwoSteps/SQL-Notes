### DDL ：

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

#### 一、创建表：CREATE TABLE

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

#### 二、创建VIEW

__什么是视图：__
视图其实就是一条查询sql语句，用于显示一个或多个表或其他视图中的相关数据。视图将一个查询的结果作为一个表来使用，因此视图可以被看作是存储的查询或一个虚拟表，与真实表不同，视图不会要求分配存储空间，视图中也不会包含实际的数据。视图只是定义了一个查询，视图中的数据是从基表中获取，这些数据在视图被引用时动态的生成。由于视图基于数据库中的其他对象，因此一个视图只需要占用数据字典中保存其定义的空间，而无需额外的存储空间，并且基表的变化会导致视图相应的改变。

__创建方法：__
    
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

### 2、修改表：ALTER TABLE

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


### 3、DROP 相关的常用命令：

__(1) 删除表：__

    DROP TABLE table_name;

__(2) 删除索引__  

    DROP INDEX index_name;

__(3) 删除视图__

    DROP VIEW [IF EXISTS] view_name [, view_name] ... [RESTRICT | CASCADE]

### 4、查看表结构：

    DESCRIBE tbl_name;

### 5、SHOW 相关的常用命令：

    #查看表状态信息

    SHOW [FULL] TABLES [{FROM | IN} db_name] [LIKE 'pattern' | WHERE expr] 

    最简单的用法：SHOW TABLES;

__查看表上的索引：__

    SHOW {INDEX | INDEXES | KEYS} {FROM | IN} tbl_name [{FROM | IN} db_name] [WHERE expr]   

    如：SHOW INDEX FROM score1 FROM data_analysis; 查看data_analysis库中的score1表，也可以写成 SHOW INDEX FROM data_analysis.score1


### 数据表的常见约束：

__主键约束：__ 主键起唯一标识一条记录的作用，不能重复，不能为空，即 UNIQUE+NOT NULL。一个数据表的主键只能有一个。主键可以是一个字段，也可以由多个字段复合组成。命令创建主键的命令为 如PRIMARY KEY (`player_id`) USING BTREE 意为将player_id设置为主键，索引采用BTREE索引,在Navicat中可以在索引标题栏中写明。

__外键约束：__ 外键确保了表与表之间引用的完整性。一个表中的外键对应另一张表的主键。外键可以是重复的，也可以为空。

__唯一性约束：__ 唯一性约束表明了字段在表中的数值是唯一的，即使我们已经有了主键，还可以对其他字段进行唯一性约束。比如我们创建 player 表并给 player_name 设置唯一性约束，就表明任何两个球员的姓名不能相同。需要注意的是，唯一性约束和普通索引（NORMAL  INDEX）之间是有区别的。__唯一性约束相当于创建了一个约束和普通索引，目的是保证字段的正确性，而普通索引只是提升数据检索的速度，并不对字段的唯一性进行约束。__

__NOT NULL约束：__ 对字段定义了 NOT NULL，即表明该字段不应为空，必须有取值。

__DEFAULT约束：__ DEFAULT，表明了字段的默认值。如果在插入数据的时候，这个字段没有取值，就设置为默认值。比如我们将身高 height 字段的取值默认设置为 0.00，即DEFAULT 0.00

__CHECK约束：__ CHECK 约束，用来检查特定字段取值范围的有效性，CHECK 约束的结果不能为 FALSE，比如我们可以对身高 height 的数值进行 CHECK 约束，必须≥0，且＜3，即CHECK(height>=0 AND height<3)




