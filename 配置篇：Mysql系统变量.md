# 系统变量

对于大部分系统变量来说，它们的值可以在服务器程序运行过程中进行动态修改而无需停止并重启服务器。

`注意: ` 在 Mysql 命令行中设置的变量仅当次启动有效，服务重启将会失效，所以尽量保存于配置文件中。

## 一、MySQL 系统变量

我们可以使用下列命令查看MySQL服务器程序支持的系统变量以及它们的当前值：

    
    SHOW VARIABLES [LIKE 匹配的模式];   # LIKE表达式后边可以跟通配符来进行模糊查询

`注意：` 系统变量有作用范围之分，`SHOW VARIABLES` 语句默认查看的是 `SESSION` 作用范围的系统变量。

我们可以通过指定 `GLOBA`L 或者 `SESSION` 来查看指定的作用范围的系统变量，如：

    SHOW [GLOBAL|SESSION] VARIABLES [LIKE 匹配的模式];



### 案例：常用的查看系统变量的命令

*   __1、查看Mysql系统默认存储引擎：__

        SHOW VARIABLES LIKE 'default_storage_engine';

*   __2、查看Mysql系统默认最大连接数：__

        SHOW VARIABLES like 'max_connections';

## 二、MySQL 状态变量

MySQL服务器程序中维护了好多关于程序运行状态的变量，它们被称为状态变量，我们可以通过它们了解程序的运行状态，如：

    Threads_connected：表示当前有多少客户端与服务器建立了连接。

    Handler_update：表示已经更新了多少行记录

具体的查看方法 (状态变量同样存在全局和会话变量) ：

    SHOW [GLOBAL|SESSION] STATUS [LIKE 匹配的模式];

案例展示：

    SHOW STATUS LIKE 'thread%';

## 三、设置 MySQL 系统变量

### 3.1、设置系统变量

__3.1.1、通过命令行添加启动选项__


    # 比如在启动服务器程序时指定系统变量：
    mysqld --default-storage-engine=MyISAM --max-connections=100

`需要注意的是：`使用指定参数的 = 号前后不能留有空格。

__3.1.2、通过配置文件添加启动选项__

    [server]
    default-storage-engine=MyISAM
    max-connections=10

__3.1.3、设置全局变量和会话变量__

*   1、SET [GLOBAL|SESSION] 系统变量名 = 值;

        # 设置 GLOBAL 的 default_storage_engine 变量为 MyISAM
        SET GLOBAL default_storage_engine = MyISAM

        # 设置 SESSION 的 default_storage_engine 变量为 MyISAM
        SET SESSION default_storage_engine = MyISAM;

        # 设置 SESSION 的 default_storage_engine 变量为 MyISAM
        SET default_storage_engine = MyISAM;


*   2、SET [@@(GLOBAL|SESSION).]var_name = XXX;

        # 设置 GLOBAL 的 default_storage_engine 变量为 MyISAM
        SET @@GLOBAL.default_storage_engine = MyISAM;


    __`小提示：`__ 如果某个客户端改变了某个系统变量在 `GLOBAL` 作用范围的值，并不会影响该系统变量在当前已经连接的客户端作用范围为 `SESSION` 的值，只会影响后续连入的客户端在作用范围为 `SESSION` 的值。



*   __`注意事项 1：`__ 并不是所有系统变量都具有GLOBAL和SESSION的作用范围。
    
    *   有一些系统变量只具有GLOBAL作用范围，比方说max_connections，表示服务器程序支持同时最多有多少个客户端程序进行连接。

    *   有一些系统变量只具有SESSION作用范围，比如insert_id，表示在对某个包含AUTO_INCREMENT列的表进行插入时，该列初始的值。

    *   有一些系统变量的值既具有GLOBAL作用范围，也具有SESSION作用范围，比如我们前边用到的default_storage_engine，而且其实大部分的系统变量都是这样的，

*   __`注意事项 2：`__ 有些系统变量是只读的，并不能设置值。

    *   比方说version，表示当前MySQL的版本，我们客户端是不能设置它的值的，只能在SHOW VARIABLES语句里查看。


### 3.2、启动变量和系统变量

*   大部分的系统变量都可以被当作启动选项传入。

*   但是有些系统变量是不能作为启动参数来设置的，如character_set_client等。

*   有些启动选项也不是系统变量，比如defaults-file。



## 四、MySQL 系统变量存在作用范围

在服务器启动时，会将每个全局变量初始化为其默认值（可以通过命令行或选项文件中指定的选项更改这些默认值）。

然后服务器还为每个连接的客户端维护一组会话变量，客户端的会话变量在连接时使用相应全局变量的当前值初始化。


*   全局变量：影响服务器的整体操作。


*   session变量：影响某个客户端连接的操作。（注：SESSION有个别名叫LOCAL）

    系统变量 `default_storage_engine` 是针对 `session` 的，客户端A可以设置 `default_storage_engine` 为 `Inoodb` ，客户端B可以将 `default_storage_engine` 设置为 `MyISAM` 二者互相独立，互不影响。

`一个简单的分辨全局变量和会话变量的方法：`通过启动选项设置的系统变量的作用范围都是`GLOBAL`的，也就是对所有客户端都有效的。



