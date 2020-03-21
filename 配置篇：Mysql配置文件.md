# 配置文件解读

## 一、Mysql配置文件查找路径优先级：

`/etc/my.cnf --> /etc/mysql/my.cnf --> $MYSQL_HOME/my.cnf --> --default-extra-file=/path/to/somedir/my.cnf --> ~/.my.cnf`

不论文件有没有存在，配置文件都会加载进来，如果一个参数的值在不同配置文件中定义不同的，__后面的优先级更高__。

如果我们不想让MySQL到默认的路径下搜索配置文件，可以在命令行指定defaults-file选项，比如这样：
    
    mysqld --defaults-file=/tmp/myconfig.cnf

这里注意：`defaults-extra-file` 和 `defaults-file` 的区别，使用 `defaults-extra-file ` 可以指定额外的配置文件搜索路径（也就是说那些固定的配置文件路径也会被搜索）。


## 二、配置文件：my.cnf

配置文件中的启动选项被划分为若干个组，每个组有一个组名，用中括号[]扩起来，如：

`以下组的启动选项将作用于所有的服务器程序。`

    [server]                        
    innodb_file_per_table=0         # 当innodb_file_per_table的值为0时，代表新建表使用系统表空间；
                                    # 当innodb_file_per_table的值为1时，代表新建表使用独立表空间。


    skip-networking                 # 跳过网络连接
    default-storage-engine=MyISAM   # 默认引擎为MyISAM存储引擎

    [mysqld]
    (具体的启动选项...)

    datadir=/var/lib/mysql
    socket=/var/lib/mysql/mysql.sock

    [mysqld_safe]
    (具体的启动选项...)

    log-error=/var/log/mariadb/mariadb.log
    pid-file=/var/run/mariadb/mariadb.pid

`以下组的启动选项将作用于所有的客户端程序。`

    [client]    
    (具体的启动选项...)

    [mysql]
    (具体的启动选项...)

    [mysqladmin]
    (具体的启动选项...)

### 特定MySQL版本的专用选项组

并且我们可以指定版本号，比如对于`[mysqld]`选项组来说，我们可以定义一个`[mysqld-5.7]`的选项组，它的含义和`[mysqld]`一样，只不过只有版本号为5.7的mysqld程序才能使用这个选项组中的选项。

## 三、不同的启动命令，调用的配置文件组不同

但是要注意的是，不同的启动mysql的命令，调用配置文件中的组也是不同的，比如：

| 启动命令 | 类别     | 能读取的组                    |
| ------------ | ---------- | ---------------------------------- |
| mysqld       | 启动服务器 | [mysqld]、[server]                |
| mysqld_safe  | 启动服务器 | [mysqld]、[server]、[mysqld_safe] |
| mysql.server | 启动服务器 | [mysqld]、[server]、[mysql.server] |
| mysql        | 启动客户端 | [mysql]、[client]                 |
| mysqladmin   | 启动客户端 | [mysqladmin]、[client]            |
| mysqldump    | 启动客户端 | [mysqldump]、[client]             |


注意：如果我们想指定`mysql.server`程序的启动参数，则必须将它们放在配置文件中，而不是放在命令行中。`mysql.server`仅支持start和stop作为命令行参数。



