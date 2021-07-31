# 查看命令
```
➜  sqoop-1.4.7.bin__hadoop-2.6.0 bin/sqoop export --help
```
# 从HDFS表导出数据到MySQL
## MySQL数据准备
```
mysql -uroot -p123456

create database db_sqoop;
use db_sqoop;

CREATE TABLE  user_info_export (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(20) DEFAULT NULL,
  `password` varchar(20) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

➜  hadoop-2.7.0 bin/hdfs dfs -text /user/cjx/sqoop/import/user_info/part-m-00000
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
```
## 导出数据(默认是增量导出)
```
bin/sqoop export \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--num-mappers 1  \
--table user_info_export \
--export-dir /user/cjx/sqoop/import/user_info \
--input-fields-terminated-by '\t'
```
## 指定导出列
指定的是MySQL的列，会用HDFS的前n列作为数据
```
bin/sqoop export \
--connect jdbc:mysql://localhost:3306/db_sqoop \
--username root \
--password 123456 \
--num-mappers 1  \
--table user_info_export \
--export-dir /user/cjx/sqoop/import/user_info \
--input-fields-terminated-by '\t' \
--columns 'username,password'

mysql> select * from user_info_export;
+----+----------+----------+
| id | username | password |
+----+----------+----------+
|  1 | admin    | admin    |
|  2 | wang     | 111111   |
|  3 | zhang    | 000000   |
|  4 | lili     | 000000   |
|  5 | henry    | 000000   |
|  6 | cherry   | 000000   |
|  7 | ben      | 111111   |
|  8 | leo      | 000000   |
|  9 | test     | test     |
| 10 | system   | 000000   |
| 11 | xiao     | 111111   |
| 12 | 1        | admin    |
| 13 | 2        | wang     |
| 14 | 3        | zhang    |
| 15 | 4        | lili     |
| 16 | 5        | henry    |
| 17 | 6        | cherry   |
| 18 | 7        | ben      |
| 19 | 8        | leo      |
| 20 | 9        | test     |
| 21 | 10       | system   |
| 22 | 11       | xiao     |
+----+----------+----------+
```