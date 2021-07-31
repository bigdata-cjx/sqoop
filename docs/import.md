# 查看命令
```
➜  sqoop-1.4.7.bin__hadoop-2.6.0 bin/sqoop import --help
```
# 从MySQL表导数据到HDFS
## MySQL数据准备
```
mysql -uroot -p123456

create database db_sqoop;
use db_sqoop;

CREATE TABLE  user_info (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(20) DEFAULT NULL,
  `password` varchar(20) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

insert into user_info values(1,'admin','admin');
insert into user_info values(2,'wang','111111');
insert into user_info values(3,'zhang','000000');
insert into user_info values(4,'lili','000000');
insert into user_info values(5,'henry','000000');
insert into user_info values(6,'cherry','000000');
insert into user_info values(7,'ben','111111');
insert into user_info values(8,'leo','000000');
insert into user_info values(9,'test','test');
insert into user_info values(10,'system','000000');
insert into user_info values(11,'xiao','111111');

select * from user_info;
```
## 数据导入
### 直接导入到HDFS
```
bin/sqoop import \
--connect jdbc:mysql://bigdata:3306/db_sqoop \
--username root \
--password 123456 \
--table user_info
```
#### 合并结果
导入数据在：http://bigdata:50070/explorer.html#/user/cjx/user_info
#### 使用HDFS绝对路径
```
➜  hadoop-2.7.0 bin/hdfs dfs -getmerge hdfs://bigdata:9000/user/cjx/user_info /opt/datas/user_info.txt 
➜  hadoop-2.7.0 cat /opt/datas/user_info.txt
1,admin,admin
2,wang,111111
3,zhang,000000
4,lili,000000
5,henry,000000
6,cherry,000000
7,ben,111111
8,leo,000000
9,test,test
10,system,000000
11,xiao,111111
```
#### 使用HDFS相对路径
```
➜  hadoop-2.7.0 bin/hdfs dfs -getmerge user_info /opt/datas/user_info2.txt 
➜  hadoop-2.7.0 cat /opt/datas/user_info2.txt
1,admin,admin
2,wang,111111
3,zhang,000000
4,lili,000000
5,henry,000000
6,cherry,000000
7,ben,111111
8,leo,000000
9,test,test
10,system,000000
11,xiao,111111
```
### 指定目录、mapper数量
--num-mappers 1 
```
bin/sqoop import \
--connect jdbc:mysql://bigdata:3306/db_sqoop \
--username root \
--password 123456 \
--table user_info \
--delete-target-dir \
--num-mappers 1  \
--target-dir /user/cjx/sqoop/import/user_info

http://bigdata:50070/explorer.html#/user/cjx/sqoop/import/user_info
```
### 加速
--direct
>> 21/07/31 17:57:31 WARN manager.MySQLManager: It looks like you are importing from mysql.
>> 21/07/31 17:57:31 WARN manager.MySQLManager: This transfer can be faster! Use the --direct
>> 21/07/31 17:57:31 WARN manager.MySQLManager: option to exercise a MySQL-specific fast path.
```
bin/sqoop import \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--table user_info \
--delete-target-dir \
--num-mappers 1  \
--target-dir /user/cjx/sqoop/import/user_info2 \
--direct

http://bigdata:50070/explorer.html#/user/cjx/sqoop/import/user_info

➜  hadoop-2.7.0 bin/hdfs dfs -text /user/cjx/sqoop/import/user_info2/part-m-00000
1,admin,admin
2,wang,111111
3,zhang,000000
4,lili,000000
5,henry,000000
6,cherry,000000
7,ben,111111
8,leo,000000
9,test,test
10,system,000000
11,xiao,111111
```
#### 报错处理
Error: java.io.IOException: Cannot run program "y": error=2, 没有那个文件或目录

解决方法：sqoop所在的机器安装MySQL
### 指定生成的文件，使用的分隔符
--fields-terminated-by '\t'
```
bin/sqoop import \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--table user_info \
--delete-target-dir \
--num-mappers 1  \
--target-dir /user/cjx/sqoop/import/user_info \
--fields-terminated-by '\t'

➜  hadoop-2.7.0 bin/hdfs dfs -text /user/cjx/sqoop/import/user_info/part-m-00000
```
### 存储格式
--as-parquetfile 
``` 
bin/sqoop import \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--table user_info \
--delete-target-dir \
--num-mappers 1  \
--target-dir /user/cjx/sqoop/import/user_info \
--fields-terminated-by '\t' \
--as-parquetfile 
```
### 数据压缩
--compress \
--compression-codec org.apache.hadoop.io.compress.SnappyCodec
```
bin/sqoop import \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--table user_info \
--delete-target-dir \
--num-mappers 1  \
--target-dir /user/cjx/sqoop/import/user_info \
--fields-terminated-by '\t' \
--compress \
--compression-codec org.apache.hadoop.io.compress.SnappyCodec

➜  hadoop-2.7.0 bin/hdfs dfs -text /user/cjx/sqoop/import/user_info/part-m-00000.snappy
```
### 数据选择
#### 选择列
--columns 'username,password'
#### 导入模式（需要指定根据哪个字段判断是否是增加的数据：--check-column 'id'）
--incremental append
#### 根据SQL语句（必须使用where关键字，并且需要指定条件为：$CONDITIONS）
--query 'select * from user_info where id > 5 and $CONDITIONS'
```
bin/sqoop import \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--columns 'username,password' \
--num-mappers 1  \
--target-dir /user/cjx/sqoop/import/user_info \
--fields-terminated-by '\t' \
--check-column 'id' \
--incremental append \
--query 'select * from user_info where id > 5 and $CONDITIONS'
```
