MySQL 用户管理
---
> 随着深入，MySQL可以通过用户管理完成对内部数据的访问控制。

# 1. 新建用户
> CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

1. 用户名称:`username`
2. 登录主机:`localhost`，可以替换成具体的IP地址，全部允许:`%`
3. 口令:`password`

# 2. 删除用户
> DROP USER username@'localhost';

# 3. 为用户分配权限

## 3.1. 具体数据库权限
> grant all privileges on \`test_db\`.* to 'username'@'localhost';

1. 授权给用户username，数据库test_db相关的所有权限，并且该用户username在所有网络IP上都有权限，%是指没有网络限制
2. 权限可以细分为：`SELECT`,`INSERT`,`UPDATE`,`DELETE`,`CREATE`,`DROP`,更多权限见参考二

## 3.2. 刷新权限
> flush privileges;

## 3.3. 授予管理员权限
> grant all privileges on \*.\* to 'username'@'password' IDENTIFIED BY 'password' with grant option;

1. \*.\*
   1. 第一个\*表示是所有数据库
   2. 第二个\*表示是所有数据表
2. `with grant option`是指允许用户username，传递其拥有的权限给其他的用户

# 4. 收回用户权限
> revoke grant option on *.* from username;

# 5. 查看某个用户的权限
> show grants for username@localhost;

# 6. 查看用户表
> SELECT * FROM mysql.\`user\`

# 7. 参考
1. <a href = "https://jingyan.baidu.com/article/a948d651cadd340a2dcd2e88.html">mysql数据库怎么新建用户</a>
2. <a href = "https://www.cnblogs.com/faberbeta/p/mysql002.html">MySQL 新建用户，为用户授权，指定用户访问数据库</a>