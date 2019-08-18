# python连接mysql


### 下载包

    pip install mysql-connector

### 基本使用语法

    # -*- coding: UTF-8 -*-
    import mysql.connector
    
    # 打开数据库连接
    db = mysql.connector.connect(
           host="localhost",
           user="root",
           passwd="XXX", # 写上你的数据库密码
           database='wucai', 
           auth_plugin='mysql_native_password'
    )
    # 获取操作游标 
    cursor = db.cursor()   
    
    # 定义 SQL 语句
    sql= SELECT user_name FROM %s WHERE user_id=%s
    
    # 用于补充 SQL 语句中的%s
    val=(user,1) 
    
    # 执行 SQL 语句
    cursor.execute(sql,val)
    
    # 或者直接执行 SQL 语句 如下
    cursor.execute("SELECT VERSION()")
    
    # 获取一条数据
    data = cursor.fetchone()
    
    print("MySQL 版本: %s " % data)
    
    # 关闭游标 & 数据库连接
    cursor.close()
    db.close()

*   __Connection 就是对数据库的当前连接进行管理，我们可以通过它来进行以下操作：__

    通过指定 host、user、passwd 和 port 等参数来创建数据库连接，这些参数分别对应着数据库 IP 地址、用户名、密码和端口号；
    
    使用 db.close() 关闭数据库连接；
    
    使用 db.cursor() 创建游标，操作数据库中的数据；
    
    __使用 db.begin() 开启事务；__
    
    __使用 db.commit() 和 db.rollback()，对事务进行提交以及回滚。__
  
*   __创建游标之后对数据库进行操作__

    cursor.execute(query_sql) ，执行数据库查询；
    
    cursor.fetchone()，读取数据集中的一条数据；
    
    cursor.fetchall()，取出数据集中的所有行，返回一个元组 tuples 类型；
    
    cursor.fetchmany(n)，取出数据集中的多条数据，同样返回一个元组 tuples；
    
    cursor.rowcount，返回查询结果数据的行数。如果没有查询到数据或者还没有查询，则结果为 -1，否则会返回查询得到的数据行数；
    
    cursor.close()，关闭游标。
    
    
### SQL异常处理：可以使用 try... except...

导入异常捕获包

    import traceback

    except Exception as e:
        # 打印异常信息
        traceback.print_exc()
    






