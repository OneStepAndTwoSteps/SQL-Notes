# metadata lock


MDL是在5.5才引入到mysql，之前也有类似保护元数据的机制，只是没有明确提出MDL概念而已。但是5.5之前版本(比如5.1)与5.5之后版本在保护元数据这块有一个显著的不同点是，5.1对于元数据的保护是语句级别的，5.5对于metadata的保护是事务级别的。所谓语句级别，即语句执行完成后，无论事务是否提交或回滚，其表结构可以被其他会话更新；而事务级别则是在事务结束后才释放MDL。

引入MDL后，主要解决了2个问题，一个是事务隔离问题，比如在可重复隔离级别下，会话A在2次查询期间，会话B对表结构做了修改，两次查询结果就会不一致，无法满足可重复读的要求；另外一个是数据复制的问题，比如会话A执行了多条更新语句期间，另外一个会话B做了表结构变更并且先提交，就会导致slave在重做时，先重做alter，再重做update时就会出现复制错误的现象。


#### metadata lock 会导致对该表的操作阻塞

### 造成matadata lock的原因

1、表上存在未提交的事务，导致alter等待MDL
    
    这个时候如果事务一直没有进行提交，那么我们对该表的所有操作都会被阻塞。

2、大查询或mysqldump导致alter等待MDL


### 解决方法

1、提交事务，或者杀死相关的线程


#### show full processlist;

show full processlist可以显示用户正在运行的线程，需要注意的是，除了 root 用户能看到所有正在运行的线程外，其他用户都只能看到自己正在运行的线程，看不到其它用户正在运行的线程。除非单独个这个用户赋予了PROCESS 权限。


#### select * from performance_schema.metadata_locks\G

performance_schema.metadata_locks表记录一些Server层的锁信息（包括全局读锁和MDL锁等），下面我们通过一个示例来演示然后使用performance_schema来找出谁持有全局读锁。
root@localhost : (none) 01:01:43> select * from performance_schema.metadata_locks where OWNER_THREAD_ID!=sys.ps_thread_id(connection_id())\G;

    *************************** 1. row ***************************
        OBJECT_TYPE: GLOBAL
        OBJECT_SCHEMA: NULL
        OBJECT_NAME: NULL
    OBJECT_INSTANCE_BEGIN: 140621322913984
            LOCK_TYPE: SHARED  # 共享锁
        LOCK_DURATION: EXPLICIT  # 显式
        LOCK_STATUS: GRANTED  # 已授予
            SOURCE: lock.cc:1110
    OWNER_THREAD_ID: 94 # 持有锁的内部线程ID为94
    OWNER_EVENT_ID: 16  

#### kill 

根据线程的id 杀死线程，之后就可以执行被锁住的操作
