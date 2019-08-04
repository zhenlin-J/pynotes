**用来处理特殊应用场景** <br>
**视图 修改数据库** <br>
**事务 多个表并发服务器** <br>
**索引 大数据中快速查询** <br>

### 视图(用来查询数据) <br>
对于复杂问题的查询，往往有很多个数据表进行关联查询，如果数据库因为需求发生了改变，为了保证查询出来的数据和之前相同，需要在多个地方进行修改，维护起来非常麻烦。 <br>
为了解决上面的办法，我们需要定义视图。 <br>
通俗的讲，视图就是一条SELECT语句执行后返回的结果集。 <br>
视图是对若干张基本表的引用，一张虚表，查询语句执行的结果。 <br>

😂😂😂 <br>
上面写了一堆，有关视图的概念还是看的不太懂，但是实际上在前面的学习当中已经遇到并且使用过了视图这个概念。在学习 join into on ... 的语法的时候曾经将两个表连接在了一起，生成了一个新的虚拟的表，这个虚拟的表并不存在于数据库中，这个新的虚拟的表就是视图。定义一个视图可以很方便的按照实际需求来筛选信息。 <br>
下面开始演示怎样创建并使用视图： <br>
创建虚拟的表： <br>
```SQL
select * from goods as g left join goods_cates as c on g.cate_id=c.id left join goods_brands as b on g.brand_id=b.id;

select g.*,c.name as cate_name,b.name as brand_name from goods as g left join goods_cates as c on g.cate_id=c.id left join goods_brands as b on g.brand_id=b.id;
```
创建视图 <br>
create view 视图名 as ...;
```SQL
create view v_goods_info as select g.*,c.name as cate_name,b.name as brand_name from goods as g left join goods_cates as c on g.cate_id=c.id left join goods_brands as b on g.brand_id=b.id;
```
这个视图并不能修改，它只是方便来查询的。 <br>

查看视图 <br>
show tables; <br>

使用视图 <br>
select ... from 视图名; <br>

删除视图 <br>
drop view 视图名; <br>
##### 视图的作用 <br>
  - 提高了重用性，类似函数
  - 对数据库进行重构，不影响程序的运行
  - 提高安全性能，可以针对不同的用户
  - 让数据更加的安全

### 事务(用于 增 删 改 数据) <br>
库的引擎必须是 innoDB 才可以使用事务，这是MySQL的默认引擎 <br>
事务是一个操作序列，这些操作要么都执行，要么都不执行，是一个不可分割的工作单元。事务中的任何一个步骤失败，必须回滚所有的步骤。 <br>

😀😀😀 <br>
事务的概念很好理解，但是实际上在之前的学习中已经接触并使用过了事务的概念，那就是使用python来操纵MySQL，在写完SQL语句后，一定要执行commit命令。才会将所有的SQL语句一起提交执行，这就是python强制使用事务的用法。其实对于命令行来执行命令时，虽然看上去并没有事务这个现象，SQL语句可以一句一句执行并不需要输入commit，这是因为mysql客户端自动补充了commit这个命令。 <br>
事务的四大特性(ACID) <br>
  - 原子性(Atomicity)
  - 一致性(Consistency)
  - 隔离性(Isolation)
  - 持久性(Durability)

开启事务(开启后执行的修改命令会维护在缓存中，而不是物理表中) <br>
begin;  /  start transaction;

提交事务(缓存中的数据变更会维护到物理表中) <br>
commit;

回滚事务(放弃缓存中变更的数据) <br>
rollback;


### 索引(用于提高查询效率) <br>
当设置主键和外键时，就已经创建了索引。 <br>
小的表一般不加索引，这样会占空间，而且会影响更新和插入的速度，大的表也只对某些字段添加索引。 <br>
索引是一种特殊的文件，在innoDB数据表上的索引是表空间的一个组成部分，它们包含对数据表里所有记录的引用指针 <br>
  。 查看索引 show index from 表名
  。 创建索引 create index 索引名称 on 表名(字段名(长度)) 注意这里的长度，如果是数字，那么不加长度，字符串，一般加个等长即可。
  。 删除索引 drop index 索引名称 on 表名

索引原理： <br>
为什加了索引就会变快呢？我们在设置索引的时候并没有增加很犀利的参数，算法，查询方法，仅仅是添加了索引，速度就会相差两三个数量级，为什么？ <br>
这个差异来源于数据结构，MySQL是B-Tree的数据结构，本质上是通过添加数值型数据，进行分段查询来达到极大提高查询效率，然后将数值型数据对应的字段的内容显示出来。 <br>

#### 账户管理 <br>
MySQL账户体系： <br>
  。 服务实例级账户： root
  。 数据库级别账户
  。 数据表级别账户
  。 字段级别的账户
  。 储存程序级别的账户

查看所有用户 <br>
desc user; 这个命令在MySQL库里面的user表里，最好不要去动它。 <br>

创建账户&授权(需要root权限) <br>
grant 权限列表 on 数据库 to "用户名"@"访问主机" indentified by "密码"； <br>

  - 访问主机通常设置为% ，表示此账户可以在任何ip的主机上访问此数据库
  - 访问主机设定为localhost或者具体的ip，表示只允许本机或者特定主机访问
  - 常用的权限有 create alter drop insert update delete select
  - 分配所有权限 all privileges

修改权限 <br>
grant 权限名称 on 数据库 to 账户@主机 with grant option; <br>
flush privileges; 刷新权限 <br>

修改密码 <br>
update user set authentication_string=password('新密码') where user="用户名"; <br>
flush privileges; 刷新权限 <br>

远程登陆(危险慎用) <br>

删除账户 <br>
drop user '用户名'@'主机' <br>

[忘记root密码后免密登陆数据库](https://blog.csdn.net/lxpbs8851/article/details/10895085) <br>
[修改数据库](https://blog.csdn.net/lxpbs8851/article/details/8127113) <br>

### MySQL主从 <br>
在实际开发中，只有一台服务器是不够的，为了应对各种突发情况，往往需要准备多个服务器，分为主服务器和从服务器。 <br>
主从服务器实际上是对读写操作的分离，从服务器一般处理读取操作，主服务器一般处理写操作。 <br>

备份 <br>
mysqldump -uroot -p密码 数据库名 > xxx.sql; <br>
mysqldump -uroot -p密码 --all-databases --lock-all-tables > 路径/master_db.sql <br>
--all-databases 导出所有数据库  |  --lock-all-tables 锁住所有表，防止导入时有数据写入  |  ’>‘ 表示重定向输出 <br>

恢复 <br>
mysql -uroot -p密码 新数据库名 < xxx.sql; <br>
’<‘ 表示重定向输入 <br>

配置主服务器和其他的部分，参考下面的🔗 <br>
[windows10 Mysql的主从配置](https://blog.csdn.net/wangli1281/article/details/81134476) <br>
[mysql主从复制的理解和搭建](https://blog.csdn.net/li_lening/article/details/81878163) <br>