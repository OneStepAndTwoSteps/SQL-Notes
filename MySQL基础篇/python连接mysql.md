# python连接mysql

## 普通方法：

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
    
## orm方法：

如何使用 sqlalchemy 对数据库进行操作


### 下载包

    pip install sqlalchemy

### 导入包

    # 数据库连接包
    from sqlalchemy import create_engine

    # 定义列数据的方法
    from sqlalchemy import Integer,Float,Column,String

    # sqlalchemy基类
    from sqlalchemy.ext.declarative import declarative_base

    # 用于建立会话
    from sqlalchemy.orm import sessionmaker

    # 或方法
    from sqlalchemy import or_

    # func中提供了函数方法
    from sqlalchemy import func


### 创建连接对象

    # 传入数据库名, 用户名, 密码以及ip, 建立连接数据库的引擎engine(负责与数据库驱动程序进行交互), 加入echo=True则会打印创建过程
    engine=create_engine('mysql+mysqldb://sql_user:sql_user_pass@ip:3306/databases?charset=utf8')

    # 通过继承sqlalchemy生成的基类来建立与数据库表对应的类(一个类在数据库中表现为一张表)
    Base = declarative_base()


### 创建表结构

    # 自定义类, 本质就是设置数据库对应表中字段格式
    class Player(Base):
        # 设置表名, 固定格式
        __tablename__='player'
        
        # 表的结构
        # 默认就是自增长, 不加autoincrement=True参数也可
        player_id=Column(Integer,primary_key=True,autoincrement=True)
        team_id=Column(Integer)
        player_name=Column(String(255))
        height=Column(Float(3,2))

### 给Base类创建一个方法(该方法可以将数据转化成字典格式)

    def to_dict(self):
    # c.name 每个列名下面的值
    return {c.name:getattr(self,c.name,None)
            # __table__.columns它将为您提供SQL字段名称，而不是您在ORM定义中使用的属性名称
           for c in self.__table__.columns}


## 操作数据库

    创建 DBSession 类型:
    DBSession = sessionmaker(bind=engine)

    创建 session 对象:
    ession = DBSession()

    将对象可以转化为 dict 类型 Base中本无 to_dict() 这里我们创建一个。
    Base.to_dict=to_dict


*   __查询数据__

    and 关系

    rows =session.query(Player).filter(Player.height > 2.01,Player.height <=2.03).all()

    或关系

    rows =session.query(Player).filter(or_(Player.height > 2.01,Player.height <=2.03)).all()

*   __添加数据__   
    
    new_player = Player(team_id = 1003, player_name = " 约翰 - 科林斯 ", height = 2.08)

    添加到 session:

    session.add(new_player)
    
*   __修改数据__

    row = session.query(Player).filter(Player.player_name=='索恩 - 马克').first()

    row.height = 2.17

    session.commit()

    session.close()

    
*   __删除数据__

    row = session.query(Player).filter(Player.player_name=='约翰 - 科林斯').first()

    session.delete(row)

    session.commit()

    session.close()

*   __调用sql函数__

    rows = session.query(Player.team_id, func.count(Player.player_id)).group_by(Player.team_id).having(func.count(Player.player_id)>5).order_by(func.count(Player.player_id).asc()).all()

    print(rows)



    # 可以使用 上面 Base 中定义的方法，将数据转成dict
    print([row.to_dict() for row in rows])

    # 提交即保存到数据库:
    session.commit()

    # 关闭 session:

    session.close()



