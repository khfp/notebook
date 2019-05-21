# JDBC使用问题

## 1. Class.forName() 显示 Unhandle Exception：ClassNotFound

&emsp;不是导入jar包问题，而是未处理异常ClassNotFound,使用try{...}catch(ClassNotFound e){}即可

## 2. 连接数据库显示：Client does not support authentication protocol requested by server

&emsp;mysql> alter user 'root'@'localhost'identified with mysql_native_password by '当前密码';
&emsp;mysql> flush privileges;

## 3. 连接数据库显示：Unknown initial character set index '255' received from server.

&emsp;在url中增加"?useUnicode=true&characterEncoding=utf8"内容，如String url = "jdbc:mysql://localhost:3306/db_cjky?useUnicode=true&characterEncoding=utf8"
