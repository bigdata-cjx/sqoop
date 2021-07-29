# sqoop
https://attic.apache.org/projects/sqoop.html
## 下载
http://archive.apache.org/dist/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
## 安装
```
tar -zxf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
```
## 配置
docs/sqoop-env.sh
## 测试
```
➜  bin ./sqoop help

Available commands:
  codegen            Generate code to interact with database records
  create-hive-table  Import a table definition into Hive
  eval               Evaluate a SQL statement and display the results
  export             Export an HDFS directory to a database table
  help               List available commands
  import             Import a table from a database to HDFS
  import-all-tables  Import tables from a database to HDFS
  import-mainframe   Import datasets from a mainframe server to HDFS
  job                Work with saved jobs
  list-databases     List available databases on a server
  list-tables        List available tables in a database
  merge              Merge results of incremental imports
  metastore          Run a standalone Sqoop metastore
  version            Display version information

See 'sqoop help COMMAND' for information on a specific command.

➜  sqoop-1.4.7.bin__hadoop-2.6.0 cp /opt/modules/apache-hive-2.3.4-bin/lib/mysql-connector-java-5.1.27-bin.jar lib 

➜  sqoop-1.4.7.bin__hadoop-2.6.0 bin/sqoop list-databases \
--connect jdbc:mysql://bigdata:3306 \
--username root \
--password 123456 

information_schema
metastore
mysql
performance_schema
sys
```
