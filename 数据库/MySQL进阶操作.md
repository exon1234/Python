# MySQL进阶操作

## 1. 查询语句如何操作

![img](https://static001.geekbang.org/resource/image/0d/d9/0d2070e8f84c4801adbfa03bda1f98d9.png)

+ **连接器**:负责客户端与数据库的连接管理,即登录.此时连接读取的权限不会再改变,即使管理员修改也不会刷新.默认8小时无操作断开连接

  建立连接过程复杂,要减少连接动作,长时间连接会增加内存,需要刷新重连,或者重置状态mysql_reset_connection

```
show processlist #查看连接的用户
```

+ 缓存:8.0版本缓存功能已经删除,查询语句 查询结果会以key-value形式存储,当数据修改时,缓存会被清空,因次很少用.

```
# 将参数query_cache_type设置成DEMAND，这样对于默认的SQL语句都不使用查询缓存。
# 用SQL_CACHE显式指定使用缓存
mysql> select SQL_CACHE * from T where ID=10；
```

+ **分析器** :识别SQL语句,MySQL从你输入的"select"这个关键字识别出来，这是一个查询语句。做完识别后分析该SQL语句是否符合规则.
  + 在这一步会判断出是否有不存在的字段,有则报错
+ **优化器**:在有多个索引时,判断如何查询性能最好
  + 既可以先从表t1里面取出c=10的记录的ID值，再根据ID值关联到表t2，再判断t2里面d的值是否等于20。
  + 也可以先从表t2里面取出d=20的记录的ID值，再根据ID值关联到t1，再判断t1里面c的值是否等于10。

```
select * from t1 join t2 using(ID)  where t1.c=10 and t2.d=20;
```

+ **执行器**: 执行语句,先进行权限判断,根据表的引擎定义,使用引擎提供的接口.
  + 执行从第一行开始,逐一循环判断,最后将所有符合的结果返回.

## 2.更新语句如何操作

+ 过程与查询相同,在执行器步骤上InnoDB引擎是将更改记录到redo log上,在合适的时间将修改同步到内存中.在更新时会记录一条回滚操作

**redo log**的记录时循环写的,当写满后前面的会擦除重写,

```
update T set c=c+1 where ID=2;
```

+ **binlog**	:server层的日志记录,一直写入,执行更新时,先写如redo log中,然后写入binlog,最后redo log的操作才会提交.

## 3.事务

+ + 显式启动事务语句， begin 或 start transaction。配套的提交语句是commit，回滚语句是rollback。开启事务后,开始执行第一个命令才是真正启动事务.
  + set autocommit=0，这个命令会将这个线程的自动提交关掉。意味着如果你只执行一个select语句，这个事务就启动了，而且并不会自动提交。这个事务持续存在直到你主动执行commit 或 rollback 语句，或者断开连接。
+ 开启事务后,读取的数据是开启时创建的快照,而更新时读取的是最新数据.

## 4.索引

+ 基于非主键索引的查询需要多扫描一棵索引树

+ ```
  create table T(
  id int primary key, 
  k int not null, 
  name varchar(16),
  index (k))engine=InnoDB;
  ```

  + 如果语句是select * from T where ID=500，即主键查询方式，则只需要搜索ID这棵B+树；
  + 如果语句是select * from T where k=5，即普通索引查询方式，则需要先搜索k索引树，得到ID的值为500，再到ID索引树搜索一次。这个过程称为回表。

![img](https://static001.geekbang.org/resource/image/dc/8d/dcda101051f28502bd5c4402b292e38d.png)

索引只与ID绑定,例如将身份证号设为主键,姓名等为索引,加快用姓名查询速度.减少回表.

​		

## 5.加锁

使用unlock tables 解锁

+ 全局锁:Flush table with read lock 整个库只读
  + 全局锁定,数据库出问题后再恢复会自动释放锁,set global readonly=True全局锁对super无效

+ 表级锁:lock tables 表明 read/write;
  + 另一种表级锁:meta data lock数据库自动添加,对表的操作都需要申请MDL锁,因次可能发生阻塞.如果某个表上的查询语句频繁，而且客户端有重试机制，也就是说超时后会再起一个新session再请求的话，这个库的线程很快就会爆满。
    + ALTER TABLE tbl_name WAIT N add column ... 设置等待时间,阻塞自动释放锁
+ 行锁:各引擎自己实现的,myisam引擎不支持行锁,行锁在事务结束后才会解锁,并不是执行完就解锁,容易死锁.设置等待时间innodb_lock_wait_timeout

