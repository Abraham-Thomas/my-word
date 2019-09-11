MySQL是应用最广泛、普及度最高的开源关系型数据库，由瑞典MySQLAB公司开发，最后被Oracle收购。

**版本**

1996，1.0诞生

1999，MySQL AB公司成立

2000，4.0发布

2003，划时代的5.0发布，增加了事务机制。

2008，SUN公司收购

2009，Oracle收购SUN，所以MySQL属于它了。



### 登录

前提已经安装好了MySQL，并且在环境变量中配置了bin的路径，这时命令行中可以使用MySQL的指令。

```mysql
登录：mysql -uroot -p ***
执行show databases，展示数据库的逻辑空间
重设root密码：ALTER USER 'root'@'localhost' IDENTIFIED BY 'xxx';
第二种重设，通过powershell管理员身份进行
第三种重设，修改my.ini文件，删除之前添加的参数
```

图形化界面：navicat

SQL语句创建逻辑空间：

```
CREATE DATABASE test;
```



### MySQL的配置文件

在my.ini文件中，可以设置各种MySQL配置，大致分为三块：client，mysql，mysqlId