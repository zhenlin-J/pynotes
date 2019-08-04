```SQL
-- 数据库的操作
外界的SQL数据导入：切换到.SQL文件的目录下，使用source 文件名即可
	-- 链接数据库
	配置好环境变量 
	将MySQL服务打开，打开命令行
	mysql -uroot -p
	mysql -uroot -p9527

	-- 退出数据库
	exit/quit/ctrl+d

	-- 查看所有数据库
	Commands end with ; or \g
	不区分大小写
	可以换行写
	show databases;

	-- 查看当前数据库时间
	select now();

	-- 显示数据库版本
	select version()；

	-- 创建数据库
	create database 库名；
	show create database 库名;
	这里发现默认的编码是latin1，需要修改

	-- 修改数据库编码格式
	create database 库名 charset=utf8;
	utf8不要写成utf-8

	-- 删除数据库
	drop database 库名;
	如果库的名字含有特殊字符，比如-，可以在库的名字上面加上`库名` 波浪符号
	-- 删除表
	drop table 表名

	-- 查看当前使用数据库
	select database();

	-- 使用数据库
	use 库名;

-- 数据表的操作
	-- 查看当前数据库中的表
	show tables;

	-- 创建表
	create table 表名 (字段,类型,约束[, 字段 类型 约束][, 字段 类型 约束]);
	create table xxxx (id int, name varchar(20));
	create table yyyy (id int primary key not null auto_increment, name varchar(20));
	create table students(
		id int unsigned not null auto_increment primary key,
		name varchar(10),
		age tinyint unsigned,
		hight decimal(5,2),
		gender enum("男","女","保密") default "保密",
		class_id int unsigned
	);
	这里的unsigned表示没有符号，也就是正数的意思，这个参数要放在数据类型的后面使用，也就是int等才行
	-- 插入数据
	insert into students values(0, "老王", 18, 188.89, "男", 0); 
	-- 查看表的所有数据
	select * from students


	-- 查看表的结构
	desc 表名;

	-- 查看创建表的方式
	show create table students;

	-- 修改表结构 添加 删除 修改
	添加
	alter table students add birthday datetime;
	修改
	  -- 不重命名
	  alter table students modify birthday date;
	  -- 重命名
	  alter table students change birthday birth date default "2000-01-01";
	删除 (不要随便删除)
	alter table students drop hight;


上面的对库和表的操作一般用的不多，主要是对表内的数据进行增删改查

-- 表内数据的增删改查 curd
	增加
	-- 全部添加
	insert into class values(0, "laaaa");
	枚举可以用位置来代替枚举的值，简化插入的步骤
	-- 部分添加
	insert into students (name,gender) values ("中鸡", 2),("大鸡",1);
	缺省值填默认值，如果没有默认值，那么null

	修改
	update students set gender=1; 这样会全部修改
	update students set gender=2 where id=5; 这样会分组修改
	update students set gender=3,name="小小鸡" where id=8; 这样会选择性修改

	删除
	物理删除 真删
	delete * from students; 清空数据表 😥 别用
	delete from students where id>5;
	删除会将id也给删除
	逻辑删除 假删
	alter table students add is_delete bit default 0;
	update students set is_delete=1 where id=3;


查询
  -- 查询 重要 使用数据库主要就是在查询
基础查询
	select * from students;
	select name,age from students;
	select name as "姓名",age as "年龄" from students;
	select students.name as "姓名",students.age as "年龄" from students;并没有看到什么简化的地方
	select s.name as "姓名",s.age as "年龄" from students as s;
	消除重复行
	select distinct gender from students;

条件查询
	select 字段名 from 表名 where 判断条件;
	比较运算符查询
	>  <  >=  <=  =  !=  (这里的等于只有一个等号，但不是赋值运算符)
	select * from students where age>=18;

	逻辑运算符查询
	and  or  not  
	select * from students where age<18 and age>30; 失败！！ 计算机执行命令应该是从左往右执行 虽然and两边是平级的 但是不能这样取值 这种情况可以用not逻辑运算符
	select * from students where age<=18 and gender="女";
	select * from students where not (age<=18 and gender="女"); 
	select * from students where not(age>=18 and age<=30);

	模糊查询 (因为要匹配 效率有点低)
	like(效率有点低): % 替换零个或一个或多个  _ 替换一个，不多不少，就一个
	select name from students where name like "%小%";
	select name from students where name like "__%"; 这个语句表示至少两个字符

	rlike 正则表达式 😭
	select name from students where name rlike "^小.*";

	范围查询
	  非连续范围
	  in (.,.,)
	  select name,age from students where age in (13,15,18,100);
	  不在非连续范围内
	  not in (.,.,)
	  select name,age from students where age not in (13,18,20,24);

	  连续的范围
	  between ... and ...  两边均闭合
	  select name,age from students where age between 15 and 18; 
	  不在非连续范围内
	  not between ... and ...
	  select name,age from students where age not between 15 and 18; 
	  select name,age from students where age (not between 15 and 18); 失败！！ 实际上这里的 not between and 是一种固定用法 不需要也不能加()
	  select name,age from students where not (age between 15 and 18); 括号可删除，这种用法就属于抛弃了固定的语法结构，采用not取反的效果，将两句话组合到一起，不建议这样使用，括号可删除的意思是这里的and优先级比not高，会优先执行and

	 空值判断
	 is null
	 select name,age from students where age is null;
	 非空判断
	 is not null
	 select name,age from students where age is not null;

排序
	order by 字段
	select id,name,age from students where (age between 10 and 30) and gender=2 order by age; 
	默认从小到大排序 可以省略asc
	从大到小排序 使用desc
	select id,name,age from students where gender=2 order by age desc;
	-- 有相同值 那么按照主键来排序
	-- 这里意外的发现 null也可以排序 而且是最小的那个
	相同值
	select id,name,age from students where gender=1 order by age desc,id desc;
	上面语句的意思是在年龄相同的情况下，再使用id降序排序，否则默认主键升序

聚合函数
	count 总数
	select count(*) from students where gender=1;
	select count(*) as 男性人数 from students where gender=1;

	max 最大值
	select max(age) as 最大年龄 from students where gender=2;

	sum 求和
	select sum(age) from students where gender=2;

	avg 算术平均值
	select avg(age) from students where gender=2;
	select sum(age)/count(age) from students where gender=2;
	-- 设置小数位数 round
	select round(avg(age),2) from students; 默认四舍五入
	在实际的生产生活中，尽量不要储存小数，因为小数会带来误差(C语言中记录小数也是≈)
	一个可行的解决方案是将小数扩大成整数来储存，比如保留两位小数就*100这样，以后在调用之后，对运算结果取/100的操作即可😥


	分组 (常与聚合函数一起使用)
	group by
	select name from students group by gender;
	上面的语句错误 分组后要在组内选取字段 而且这个字段要能唯一关联这个组 否则无法判断这个字段到底来自哪个组
	select gender from students group by gender;
	上面的语句虽然没有错误，但是也看不出来有啥子用，因为没有配合聚合函数来使用
	select gender,count(id) from students group by gender;
	select gender,avg(age) from students group by gender;

	显示分组内的内容
	group_concat(.," ",)
	select gender,group_concat(name) from students group by gender;
	select gender,group_concat(name, " ", age) from students group by gender;
	select gender,group_concat(name, " ", age) from students where id>10 group by gender;

	having 对分的组增加筛选的条件 对临时计算的结果进行判断
	select gender,group_concat(name) from students group by gender having count(id)>5;
	select gender,group_concat(name) from students group by gender having avg(age)>20;

分页
	限制查询数量，位置
	limit start(起始下标，这个与index一样，从0开始), count(计数器)
	限制查询出来的数据的个数
	select * from students limit 3; 只取前三个数据
	select * from students limit 0,3; 取0索引开始的前三个数据  
	-- 查询第一个 5个数据
	select * from students limit 0,5;
	-- 查询第二个 5个数据
	select * from students limit 5,5;
	-- 查询第三个 5个数据
	select * from students limit 10,5;
	-- 查询第四个 5个数据
	select * from students limit 15,5; 这种模式在实际的生产中非常的常见 规律也很明显、

	select * from students limit 1,10 order by age desc; 错误
	上面的语句错误，limit语句应该置于句末

连接查询
	需要的结果来源于多张表，需要将多个表连接起来取交取并进行查询
	inner join ... on
	内连接查询 取表的交集的部分  ━((*′д｀)爻(′д｀*))━!!!!
	select * from students inner join class;
	上面的语句没有添加条件，会使用students内的每一行数据去分别匹配class内的所有数据 最后的结果就是两张表的数据个数的乘积
	select * from students inner join class on students.class_id=class.id;
	还可以添加筛选字段的条件
	select students.name,class.name from students inner join class on students.class_id=class.id;
	还可以添加结果的排序条件
	select students.*,class.name from students inner join class on students.class_id=class.id order by class.name,students.id;

	lefe join ... on
	外连接查询中的左连接查询 感觉是个伪并集 右边的匹配不到左边的部分会被设置为null
	select * from students left join class on students.class_id=class.id;	
	左连接查询，查询的时候以左边的表为基准，左边的表的内容会全部显示，右边的表来匹配左边的表，匹配不到的以null来显示
	select * from students left join class on students.class_id=class.id having class.id is null;
	select * from students left join class on students.class_id=class.id where class.id is null;
	上面两个语句中使用having来筛选比较好，从查询结果中来筛选使用having 一般使用where在原表中查询

	-- right join ... on
	-- 跟上面的 left join ... on 相反 但是实际上不需要分别的来记忆，只需要将left
	select * from class left join students on class.id=students.class_id;

自关联
	一个表里面的某个字段关联表内的其他字段称为自关联
	自关联可以将多个结构一样的表整合到一张表内，然后只需要通过自关联的方法即可达成原来多张表的递归效果
	比如说省 市 区这种表 多用自关联
	没有素材只好给出代码演示了
select * from area as province inner join areas as city on province.id=city.pid having province.altile="湖北省"； (⊙x⊙;)
	很巧妙地想法啊，因为自关联只是用一张表，这里使用as将一张表当作两张表来使用

子查询(比较慢)
	查询语句的嵌套
	select * from students where age = (select max(age) from  students);
	select * from area where pid = (select id from area where altitle="湖北省");
可见达成同一个要求的SQL语句可能有很多种，但是随着数据量的增大，SQL语句需要的时间的差异会变得很明显，所以推荐使用最优化的SQL语句


practice:
part one: setup jingdong database

create database jindong charset=utf8;
use jindong;
select database(); 别他妈省这一步
create table goods(
	id int unsigned primary key auto_increment not null,
	name varchar(150) not null,
	cate_name varchar(40) not null,
	brand_name varchar(40) not null,
	price decimal(11,3) not null default 0,
	is_show bit not null default 0,
	is_saleoff bit not null default 0
);
show tables;
insert into goods values (0,"笔记本_1","笔记本","品牌_1",3399,default,default);
insert into goods values (0,"笔记本_2","游戏本","品牌_2",4399,default,default);
insert into goods values (0,"笔记本_3","超极本","品牌_3",5399,default,default);
insert into goods values (0,"笔记本_4","触控本","品牌_1",6399,default,default);
insert into goods values (0,"笔记本_5","超极本","品牌_2",8999,default,default);
insert into goods values (0,"笔记本_6","游戏本","品牌_3",1399,default,default);
insert into goods values (0,"笔记本_7","台式机","品牌_4",1999,default,default);
insert into goods values (0,"笔记本_8","电脑包","品牌_2",599,default,default);
insert into goods values (0,"笔记本_9","服务器","品牌_5",99999,default,default);
insert into goods values (0,"笔记本_10","触控本","品牌_6",8699,default,default);
insert into goods values (0,"笔记本_11","笔记本","品牌_7",6699,default,default);
insert into goods values (0,"笔记本_12","超极本","品牌_5",8699,default,default);
insert into goods values (0,"笔记本_13","笔记本","品牌_4",4699,default,default);
insert into goods values (0,"笔记本_14","台式机","品牌_2",6699,default,default);
insert into goods values (0,"笔记本_15","服务器","品牌_9",3699,default,default);
insert into goods values (0,"笔记本_16","触控本","品牌_3",9999,default,default);
insert into goods values (0,"笔记本_17","笔记本","品牌_5",4599,default,default);
insert into goods values (0,"笔记本_18","触控本","品牌_8",6699,default,default);
insert into goods values (0,"笔记本_19","游戏本","品牌_2",5699,default,default);
insert into goods values (0,"笔记本_20","超极本","品牌_9",8699,default,default);
查询每种电脑中价格最贵的电脑的信息
select g_price.cate_name,goods.name,goods.price from (
	select cate_name,max(price) as max_price from goods group by cate_name
) as g_price left join goods on (g_price.cate_name=goods.cate_name) and (g_price.max_price=goods.price);
这行SQL语句很长，但是结构很清晰，就是利用新生成的表和原有的表用left join on 做了连接查询，注意连接的顺序，这是一对多的场景，结果应该以一为准，当然如果不习惯可以两种都试试看
这个表的信息不够范式=。= 所以要进行表的拆分w(ﾟДﾟ)w

拆分表
商品分类表
create table if not exists goods_cates(
	id int unsigned primary key auto_increment not null,
	name varchar(20)
);

将分组结果导入到新表中
insert into goods_cates (name) select distinct cate_name from goods;
这里的插入语句不加value，而是使用()加上需要插入的字段的名字，但是看这样子只列级导入

同步表数据 update .. set ..;
update goods inner join goods_cates on goods.cate_name=goods_cates.name set goods.cate_name=goods_cates.id;

修改字段的名字和属性
alter table goods change brand_name brand_id int unsigned not null;

设置外键 实际开法中，很少使用外键约束，会极大地降低表更新的效率 ○|￣|_
delete from goods where cate_id=12; 删除异常值
alter table goods add foreign key (cate_id) references goods_cates(id);

再来创建另一个品牌的表
这次在创建的时候就导入数据
create table if not exists goods_brands(
	id int unsigned primary key auto_increment not null,
	name varchar(20))
	select brand_name as name from goods group by brand_name
; 
这里他妈的必须要使用分组的去重方法，不然这个重命名的指令好像无法生效
update goods inner join goods_brands on goods.brand_name=goods_brands.name set goods.brand_name=goods_brands.id;
alter table goods change brand_name brand_id int unsigned not null; 
alter table goods add foreign key (brand_id) references goods_brands(id);

删除外键 取消外键约束防止外键降低性能
要先使用下面的命令获取到外键约束名称，该名称由系统自动生成
show create table goods;
alter table goods drop foreign key goods_ibfk_1;
alter table goods drop foreign key goods_ibfk_2;



58到家数据库30条军规解读


军规适用场景：并发量大、数据量大的互联网业务

军规：介绍内容

解读：讲解原因，解读比军规更重要

一、基础规范

（1）必须使用InnoDB存储引擎

解读：支持事务、行级锁、并发性能更好、CPU及内存缓存页优化使得资源利用率更高

（2）必须使用UTF8字符集

解读：万国码，无需转码，无乱码风险，节省空间

（3）数据表、数据字段必须加入中文注释

解读：N年后谁tm知道这个r1,r2,r3字段是干嘛的

（4）禁止使用存储过程、视图、触发器、Event

解读：高并发大数据的互联网业务，架构设计思路是“解放数据库CPU，将计算转移到服务层”，并发量大的情况下，这些功能很可能将数据库拖死，业务逻辑放到服务层具备更好的扩展性，能够轻易实现“增机器就加性能”。数据库擅长存储与索引，CPU计算还是上移吧

（5）禁止存储大文件或者大照片

解读：为何要让数据库做它不擅长的事情？大文件和照片存储在文件系统，数据库里存URI多好

二、命名规范

（6）只允许使用内网域名，而不是ip连接数据库

（7）线上环境、开发环境、测试环境数据库内网域名遵循命名规范

业务名称：xxx

线上环境：dj.xxx.db

开发环境：dj.xxx.rdb

测试环境：dj.xxx.tdb

从库在名称后加-s标识，备库在名称后加-ss标识

线上从库：dj.xxx-s.db

线上备库：dj.xxx-sss.db

（8）库名、表名、字段名：小写，下划线风格，不超过32个字符，必须见名知意，禁止拼音英文混用

（9）表名t_xxx，非唯一索引名idx_xxx，唯一索引名uniq_xxx

三、表设计规范

（10）单实例表数目必须小于500

（11）单表列数目必须小于30

（12）表必须有主键，例如自增主键

解读：

a）主键递增，数据行写入可以提高插入性能，可以避免page分裂，减少表碎片提升空间和内存的使用

b）主键要选择较短的数据类型， Innodb引擎普通索引都会保存主键的值，较短的数据类型可以有效的减少索引的磁盘空间，提高索引的缓存效率

c） 无主键的表删除，在row模式的主从架构，会导致备库夯住

（13）禁止使用外键，如果有外键完整性约束，需要应用程序控制

解读：外键会导致表与表之间耦合，update与delete操作都会涉及相关联的表，十分影响sql 的性能，甚至会造成死锁。高并发情况下容易造成数据库性能，大数据高并发业务场景数据库使用以性能优先

四、字段设计规范

（14）必须把字段定义为NOT NULL并且提供默认值

解读：

a）null的列使索引/索引统计/值比较都更加复杂，对MySQL来说更难优化

b）null 这种类型MySQL内部需要进行特殊处理，增加数据库处理记录的复杂性；同等条件下，表中有较多空字段的时候，数据库的处理性能会降低很多

c）null值需要更多的存储空，无论是表还是索引中每行中的null的列都需要额外的空间来标识

d）对null 的处理时候，只能采用is null或is not null，而不能采用=、in、<、<>、!=、not in这些操作符号。如：where name!=’shenjian’，如果存在name为null值的记录，查询结果就不会包含name为null值的记录

（15）禁止使用TEXT、BLOB类型

解读：会浪费更多的磁盘和内存空间，非必要的大量的大字段查询会淘汰掉热数据，导致内存命中率急剧降低，影响数据库性能

（16）禁止使用小数存储货币

解读：使用整数吧，小数容易导致钱对不上

（17）必须使用varchar(20)存储手机号

解读：

a）涉及到区号或者国家代号，可能出现+-()

b）手机号会去做数学运算么？

c）varchar可以支持模糊查询，例如：like“138%”

（18）禁止使用ENUM，可使用TINYINT代替

解读：

a）增加新的ENUM值要做DDL操作

b）ENUM的内部实际存储就是整数，你以为自己定义的是字符串？

五、索引设计规范

（19）单表索引建议控制在5个以内

（20）单索引字段数不允许超过5个

解读：字段超过5个时，实际已经起不到有效过滤数据的作用了

（21）禁止在更新十分频繁、区分度不高的属性上建立索引

解读：

a）更新会变更B+树，更新频繁的字段建立索引会大大降低数据库性能

b）“性别”这种区分度不大的属性，建立索引是没有什么意义的，不能有效过滤数据，性能与全表扫描类似

（22）建立组合索引，必须把区分度高的字段放在前面

解读：能够更加有效的过滤数据

六、SQL使用规范

（23）禁止使用SELECT *，只获取必要的字段，需要显示说明列属性

解读：

a）读取不需要的列会增加CPU、IO、NET消耗

b）不能有效的利用覆盖索引

c）使用SELECT *容易在增加或者删除字段后出现程序BUG

（24）禁止使用INSERT INTO t_xxx VALUES(xxx)，必须显示指定插入的列属性

解读：容易在增加或者删除字段后出现程序BUG

（25）禁止使用属性隐式转换

解读：SELECT uid FROM t_user WHERE phone=13812345678 会导致全表扫描，而不能命中phone索引，猜猜为什么？（这个线上问题不止出现过一次）

（26）禁止在WHERE条件的属性上使用函数或者表达式

解读：SELECT uid FROM t_user WHERE from_unixtime(day)>='2017-02-15' 会导致全表扫描

正确的写法是：SELECT uid FROM t_user WHERE day>= unix_timestamp('2017-02-15 00:00:00')

（27）禁止负向查询，以及%开头的模糊查询

解读：

a）负向查询条件：NOT、!=、<>、!<、!>、NOT IN、NOT LIKE等，会导致全表扫描

b）%开头的模糊查询，会导致全表扫描

（28）禁止大表使用JOIN查询，禁止大表使用子查询

解读：会产生临时表，消耗较多内存与CPU，极大影响数据库性能

（29）禁止使用OR条件，必须改为IN查询

解读：旧版本Mysql的OR查询是不能命中索引的，即使能命中索引，为何要让数据库耗费更多的CPU帮助实施查询优化呢？

（30）应用程序必须捕获SQL异常，并有相应处理

总结：大数据量高并发的互联网业务，极大影响数据库性能的都不让用，不让用哟。
```