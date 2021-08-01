# 编写脚本
```
➜  sqoop-1.4.7.bin__hadoop-2.6.0 vim import.txt 
import
--connect
jdbc:mysql://localhost:3306/db_sqoop
--username
root
--password
123456
--table
user_info
--delete-target-dir
--num-mappers 
1
--target-dir
/user/cjx/sqoop/import/user_info
--fields-terminated-by
'\t'
```
# 执行脚本
```
➜  sqoop-1.4.7.bin__hadoop-2.6.0 bin/sqoop --options-file ./import.txt
```