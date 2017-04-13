---
title: java用jdbc，servelet的一个小问题
categories: exception
tags:
  - java
---

1. 在用jdbc连接数据库的时候碰见一个tomcat抛得错误，  
``java.lang.ClassNotFoundException: com.mysql.jdbc.Driver`` ，  
感觉不对，我已经在idea的Modules里面添加了mysql.jar的包，在测试的时候连接是正常的。  
原来要将mysql驱动包复制到tomcat的lib文件夹。  
2. 在连接驱动包的时候碰见这种写法``Class.forName("com.mysql.jdbc.Driver")``  
原来这种写法是注册mysql依赖包，等同于``DriverManager.registerDriver(new com.mysql.jdbc.Driver());``
3. maven：为了让依赖好管理， mybatis：为了让DML更简单，spring：为了让servlet更好用