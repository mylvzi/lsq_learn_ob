# 1.基本介绍
mysql客户端是一个简单的**sql shell**。可以输入命令和执行sql语句

登陆
mysql -uroot -p;
mysql -uroot -p mysql;  直接连接到指定数据库(省去了user)
(注意有长短格式区分)
第一个mysql:应用程序
第二个mysql:默认的数据库

# 2.mysql客户端选项
* 在后面的命令行中列出选项*

2.在后面指定要读取的配置文件中的选项
![[Pasted image 20250703221530.png]]
上面中的例子:启东市会读取上面的默认文件,完成数据库的**初始化等操作**
3.从环境变量中读区


**常用命令选项**
![[Pasted image 20250703222037.png]]

尽量还是使用**长格式**

--quick 不缓存查询结果,可能会降低服务器速度

--delimite=str。设置sql语句的分隔符

--execute   --version显示当前版本。--help

**语法规则**

-- 表示长格式
-表示短格式

mysql --help == mysql -?

严格区分大小写。-p == --password  -P == --port

![[Pasted image 20250703222758.png]]