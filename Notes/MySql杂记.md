### Shell连接MySql
``` shell
mysql -h localhost -P 3306 -u root -p123456
```
### 远程访问
``` shell
grant all privileges on *.* to 'root'@'%' identified by 'admin123123' with grant option;
flush privileges;
```
### 查询结果
1. 使用 G 把行转化成列显示1。例如，你可以将查询语句的结束符号 ; 改为 G
``` sql
select * from your_table \G
```
2. 使用pager命令来改变查询结果的显示
less是免费的开源的文件查看器
``` shell
sudo apt-get update
sudo apt-get install less
```

启用：``pager less -SFX`
停用：`nopager`

### 升级MySql
停止Docker
```shell
docker stop mysql56
```
执行升级
```shell
mysql_upgrade -uroot -p
```
查看版本
1. 使用命令提示符
```shell
mysql -V
```
2. 使用SQL查询
```sql
select version();
```

### 复制数据库
```shell
mysqldump old_db -h 192.168.1.242 -uroot -ppassword --add-drop-table | mysql new_db -uroot -ppassword
```
mysqldump中的add-drop-table参数是一个默认开启的选项。当使用mysqldump导出表结构时，如果开启了add-drop-table参数（默认情况下是开启的），那么在每个CREATE TABLE语句前，都会加上一个DROP TABLE IF EXISTS语句。这样做的目的是为了在导入数据时，如果表已经存在，就先删除原来的表，然后再创建新表并导入数据。
如果你不希望在导出的SQL文件中包含`DROP TABLE`语句，可以使用`--skip-add-drop-table`参数关闭这个功能

###  column statistics not supported by the server

在MySQL中，column-statistics是一个提供对列值的直方图统计信息的访问的表。这些信息可以用于优化查询性能。您只能查看您有一些权限的列的信息1。这个特性在mysqldump 8中默认是启用的，但是在某些情况下，可能会导致兼容性问题，比如当MySQL服务器版本低于8.0时。这就是为什么您可能会看到"column statistics not supported by the server"的错误消息。您可以通过在mysqldump命令中添加--column-statistics=0来禁用它。
```shell
mysqldump --column-statistics=0 old_db -h 192.168.1.242 -uroot -ppassword --add-drop-table | mysql new_db -uroot -ppassword
```
要默认禁用列统计，您可以在MySQL配置文件（如`/etc/my.cnf`或`~/.my.cnf`）中添加以下内容：
```txt
[mysqldump]
column-statistics=0
```