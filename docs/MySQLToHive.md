# 创建Hive元数据
```
CREATE TABLE user_info (
  id int,
  username string,
  password string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';
```
## 报错
>FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. 
>MetaException(message:An exception was thrown while adding/validating class(es) : 
>Column length too big for column 'TYPE_NAME' (max = 21845); use BLOB or TEXT instead
## 原因
Hive元数据库编码问题
## 解决
```
mysql> alter database metastore character set latin1;
```
# 从MySQL导数据到Hive
```
hive (default)> select * from user_info;
OK
user_info.id	user_info.username	user_info.password
Time taken: 0.649 seconds

bin/sqoop import \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--num-mappers 1  \
--table user_info \
--fields-terminated-by ',' \
--delete-target-dir \
--target-dir /user/hive/warehouse/user_info

hive (default)> select * from user_info;
OK
user_info.id	user_info.username	user_info.password
1	admin	admin
2	wang	111111
3	zhang	000000
4	lili	000000
5	henry	000000
6	cherry	000000
7	ben	111111
8	leo	000000
9	test	test
10	system	000000
11	xiao	111111
Time taken: 0.067 seconds, Fetched: 11 row(s)
```
mysql-table   ->  hdfs:/user/kfk/user_info  -> /user/hive/warehouse/db_hive.db/user_info
# 从Hive导数据到MySQL
```
bin/sqoop export \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--num-mappers 1  \
--table user_info_export \
--input-fields-terminated-by ',' \
--export-dir /user/hive/warehouse/user_info
```