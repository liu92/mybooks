MAC下 mysql 创建用户

1、创建用户

创建一个指定主机为127.0.0.1的用户，该用户只能在IP为127.0.0.1的主机登录，用户名叫做`test_db`，密码为`123`

```shell
mysql> create user 'test_db'@'127.0.0.1' identified by '123';
Query OK, 0 rows affected (0.14 sec)
```

2、为新用户授权

为[`test_db@127.0.0.1](mailto:`test_db@127.0.0.1)`用户授权，`grant all`表示授权所有权限

```shell
mysql> grant all on *.* to 'test_db'@'127.0.0.1' with grant option;
```



3、查看权限

```shell
mysql> show grants for 'test_db'@'127.0.0.1';
```



4、查看所有用户

```shell
mysql> select host,user from mysql.user;
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| 127.0.0.1 | test_db          |
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root             |
+-----------+------------------+
5 rows in set (0.00 sec)
```

5、查看当前用户

```shell
select current_user();
```



6、退出root用户，用刚刚创建的用户登录

```shell
#-h表示指定主机IP，-u表示指定用户名，-p表示指定密码
/mysql -h 127.0.0.1 -u test_db -p
```

7、查看当前用户

```
mysql> select current_user;
+-------------------+
| current_user      |
+-------------------+
| test_db@127.0.0.1 |
+-------------------+
1 row in set (0.00 sec)

mysql>
```

8、查看当前用户权限

```shell
mysql> show grants;
+------------------------------------------------------------------------------------------+
| Grants for test_db@127.0.0.1                                                                                                                                                                                                                                                                                                                                                                                |
+------------------------------------------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `test_db`@`127.0.0.1` WITH GRANT OPTION |
| GRANT BACKUP_ADMIN,BINLOG_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,GROUP_REPLICATION_ADMIN,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SET_USER_ID,SYSTEM_VARIABLES_ADMIN,XA_RECOVER_ADMIN ON *.* TO `test_db`@`127.0.0.1` WITH GRANT OPTION                                                                                                  |
+------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)

```



9、创建一个数据库

```
create database test_db default character set = utf8;
```



10、查看当前用户下所有数据库

```shell
show databases;
```

11、选择一个数据库mysql,并查看其中的表

```
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
```

12、再次查询一下当前用户，看看是哪个用户，发现确实是新创建的[test_db@127.0.0.1](mailto:test_db@127.0.0.1)用户，这个新创建的用户下面就已经默认有一些数据库，并且数据库里默认有一些表。

```shell
mysql> select current_user;
+-------------------+
| current_user      |
+-------------------+
| test_db@127.0.0.1 |
+-------------------+
1 row in set (0.00 sec)

mysql>
```



13、这里选择刚刚新创建的那个数据库test_db，做进一步操作

```shell
mysql> use test_db;
Database changed
```

14、这里选择刚刚新创建的那个数据库test_db，做进一步操作

```shell
mysql> select database();
+------------+
| database() |
+------------+
| test_db    |
+------------+
1 row in set (0.00 sec)
```

15、授权当前用户拥有test_db这个数据库的所有权限

```shell
mysql> grant all privileges on test_db.* to test_db@127.0.0.1;
Query OK, 0 rows affected (0.03 sec)

##刷新系统权限表
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql>
```



16、查看这个数据库中是否有表

```shell
mysql> show tables;
Empty set (0.00 sec)
```

17、创建表

```shell
##
mysql> create table student(id int auto_increment primary key,name varchar(16) not null,age int,sex char(1));
Query OK, 0 rows affected (0.12 sec)
```

表名为`student`，表中有`id,name,age,sex`四个字段，其中

字段`id`为自增长，并且为主键，数据类型为`int`，
字段`name`的数据类型为`varchar`，最大长度为16，并且不能为空，
字段`age`的数据类型为`int`，
字段`sex`的数据类型为`char`，长度为1。

数据类型`char`的长度不可变，`varchar`的长度可变。

18、**查看字符集**

```shell
show charset;
```

19、查看表结构

查看刚刚创建的表`student`的结构

```shell

mysql> describe student;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| name  | varchar(16) | NO   |     | NULL    |                |
| age   | int(11)     | YES  |     | NULL    |                |
| sex   | char(1)     | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
4 rows in set (0.01 sec)

mysql>
```



20、**查看建表语句**

```
show create table student;
mysql> show create table student;
+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                                                                                                                                                          |
+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| student | CREATE TABLE `student` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(16) NOT NULL,
  `age` int(11) DEFAULT NULL,
  `sex` char(1) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.02 sec)
```



21、查看存储引擎

```
mysql> show variables like "%engine%";
```

22、查看索引

```
mysql> show index from students;
```







23、查看innodb情况

mysql> show engine innodb status;

```shell
UFFER POOL AND MEMORY
----------------------
Total large memory allocated 137428992
Dictionary memory allocated 1829582
Buffer pool size   8191
Free buffers       6086
Database pages     2086
Old database pages 769
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 6, not young 0
0.00 youngs/s, 0.00 non-youngs/s
Pages read 1178, created 908, written 1659
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 2086, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]


（1）Total memory allocated，这就是说buffer pool最终的总⼤⼩是多少
（2）Buffer pool size，这就是说buffer pool⼀共能容纳多少个缓存页
（3）Free buffers，这就是说free链表中⼀共有多少个空闲的缓存页是可⽤的
（4）Database pages和Old database pages，就是说lru链表中⼀共有多少个缓存页，以及冷数据区域⾥的缓存页数
量
（5）Modified db pages，这就是flush链表中的缓存页数量
（6）Pending reads和Pending writes，等待从磁盘上加载进缓存页的数量，还有就是即将从lru链表中刷⼊磁盘的数
量、即将从flush链表中刷⼊磁盘的数量
（7）Pages made young和not young，这就是说已经lru冷数据区域⾥访问之后转移到热数据区域的缓存页的数量，以
及在lru冷数据区域⾥1s内被访问了没进⼊热数据区域的缓存页的数量
（8）youngs/s和not youngs/s，这就是说每秒从冷数据区域进⼊热数据区域的缓存页的数量，以及每秒在冷数据区域
⾥被访问了但是不能进⼊热数据区域的缓存页的数量
（9）Pages read xxxx, created xxx, written xxx，xx reads/s, xx creates/s, 1xx writes/s，这⾥就是说已经读取、创建
和写⼊了多少个缓存页，以及每秒钟读取、创建和写⼊的缓存页数量
（10）Buffer pool hit rate xxx / 1000，这就是说每1000次访问，有多少次是直接命中了buffer pool⾥的缓存的
（11）young-making rate xxx / 1000 not xx / 1000，每1000次访问，有多少次访问让缓存页从冷数据区域移动到了热
数据区域，以及没移动的缓存页数量
（12）LRU len：这就是lru链表⾥的缓存页的数量
（13）I/O sum：最近50s读取磁盘页的总数
（14）I/O cur：现在正在读取磁盘页的数量
```









