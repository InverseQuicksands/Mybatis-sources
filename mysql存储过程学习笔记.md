# MySQL存储过程学习笔记

## 定义

**存储过程(stored procedure)**是一组为了完成特定功能的SQL语句集合，经编译后存储在服务器端的数据库中，利用存储过程可以加速SQL语句的执行。

存储过程分为：系统存储过程和自定义存储过程。
系统存储过程：在master数据库中，但是在其他的数据库中可以直接调用，并且在调用时不必在存储过程前加上数据库名，因为在创建一个新数据库时，系统存储过程在新的数据库中会自动创建。

自定义存储过程：由用户创建并能完成某一特定功能的存储过程，存储过程既可以有参数又有返回值，但是它与函数不同，存储过程的返回值只是指明执行是否成功，并不能像函数那样被直接调用，只能利用execute来执行存储过程。

## 存储过程的优点

1. 提高应用程序的通用性和可移植性：存储过程创建后，可以在程序中被多次调用，而不必重新编写该存储过程的SQL语句。并且数据库专业人员可以随时对存储过程进行修改，且对程序源代码没有影响，这样就极大的提高了程序的可移植性。
2. 可以更有效的管理用户操作数据库的权限：在Sql Server数据库中，系统管理员可以通过对执行某一存储过程的权限进行限制，从而实现对相应的数据访问进行控制，避免非授权用户对数据库的访问，保证数据的安全。
3. 可以提高SQL的速度，存储过程是编译过的，如果某一个操作包含大量的SQL代码或分别被执行多次，那么使用存储过程比直接使用单条SQL语句执行速度快的多。
4. 减轻服务器的负担：当用户的操作是针对数据库对象的操作时，如果使用单条调用的方式，那么网络上还必须传输大量的SQL语句，如果使用存储过程，则直接发送过程的调用命令即可，降低了网络的负担。

## 创建存储过程

创建存储过程的简单语法：
```SQL
create procedure 名称()
begin
......
end
```

创建一个简单的存储过程：
```SQL
create procedure testa()
begin
    select * from student;
end;
```

调用存储过程：
```SQL
call testa();   
```
运行结果如下图所示：
<img src="images\\1.JPG" alt="结果1" style="zoom:80%; float: left"  />







删除存储过程：

```SQL
drop procedure testa；
```

查询存储过程：
```SQL
SHOW { PROCEDURE } STATUS [ LIKE  'pattern' ]
例如：
SHOW PROCEDURE STATUS LIKE 'testa';
```
运行结果如下图所示：
<img src="images\\3.JPG" alt="结果3" style="zoom:60%; float: left" />









查看存储过程的内容：

```SQL
SHOW CREATE PROCEDURE testa
```
运行结果如下图所示：
<img src="images\\4.JPG" alt="结果4" style="zoom:80%; float: left" />







## 存储过程的变量

先通过一个简单的例子来学习变量的声明和赋值
```sql
create procedure test2()
begin
  -- 使用 declare语句声明一个变量
  declare username varchar(32) default '';
  -- 使用set语句给变量赋值
  set username='xiaoxiao';
  -- 将users表中id=1的名称赋值给username
  select name into username from student where id=1;
  -- 返回变量
  select username;
end;
```

总结：
(1) 变量的声明使用declare，一句declare只声明一个变量，变量必须先声明后使用；
(2) 变量具有数据类型和长度，与mysql的SQL数据类型保持一致，因此甚至还能制定默认值、字符集和排序规则等；
(3) 变量可以通过set来赋值，也可以通过select into的方式赋值；
(4) 变量需要返回，可以使用select语句，如：select 变量名；

## 变量的作用域

变量作用域说明：
(1) 存储过程中变量是有作用域的，作用范围在begin和end块之间，end结束变量的作用范围即结束。
(2) 需要多个块之间传值，可以使用全局变量，即放在所有代码块之前
(3) 传参变量是全局的，可以在多个块之间起作用

通过一个实例来验证变量的作用域：
需求: 创建一个存储过程，用来统计表users、orders表中行数数量和orders表中的最大金额和最小金额。

```SQL
create procedure test3()
begin
  begin
    declare userscount int default 0; -- 用户表中的数量
    declare ordercount int default 0; -- 订单表中的数量
    select count(*) into userscount from users;
    select count(*) into ordercount from orders;
    select userscount,ordercount; -- 返回用户表中的数量、订单表中的数量
  end;
  begin 
    declare maxmoney int default 0; -- 最大金额
    declare minmoney int default 0; -- 最小金额
    select max(money) into maxmoney from orders;
    select min(money) into minmoney from orders;
    select maxmoney,minmoney; -- 返回最金额、最小金额
   end;
end;
```







