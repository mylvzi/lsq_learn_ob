> Redis中的字符串,直接就是按照二进制数据进行存储的(字节数组这样),所以Redis本身确实可以存储音频视频文件,不做任何**编码转换**  
> MySQL默认的字符集是拉丁文,插入中文会报错

flushall  : 清空数据库,删除Redis内存中存储的所有数据
# 基本命令
## set
**设置 key value**
* key不存在,创建新的key并存储在内存之中
* key存在,会覆盖原有的value,也可能会导致value的数据类型发生改变
set key value ex 10   设置字符串并设置超时时间
![[Pasted image 20250928154644.png]]
* NX: key不存在时才设置 -- > 不覆盖现有值
* XX:key存在时才设置 -- > 覆盖现有值
## get
指定key获取value,只支持stirng类型的value
![[Pasted image 20250928154827.png]]
![[Pasted image 20250928154935.png]]
## mset 和 mget
**一次操作多组键值对 -- > 一次网络传输**

![[Pasted image 20250928155642.png]]
![[Pasted image 20250928155307.png]]



* setnx : 不覆盖原有key, key存在就报错
* setex: 设置key的同时设置过期时间,时间为秒
* psetex : 设置毫秒级的过期时间


时间复杂度: O(N),这里的N指的是key的数量
## 计数器操作
> string的 一种编码方式是int,常用于计数器

* incr : val++
* incrby : val += n
* decr : val--
* decrby : val -= n
* incybyfloat : val +- = float

**incr**
![[Pasted image 20250928161420.png]]

* decr: 如果key不存在,会先执行  set newKey 0, 再执行  decr newKey ,返回 - 1*

![[Pasted image 20250928162502.png]]


* 在redis之中,多个客户端进行incr操作,是不会存在线程安全问题的,因为所有的命令执行都是单线程的
# append
> 追加字符串,返回追加后val的长度
> append key value


![[Pasted image 20250928172742.png]]

如何理解appeend方法的**均摊复杂度是O(1)**呢

Redis底层使用SDS(simple dynamic string)来管理字符串,这是一个自定义类型,结构如下:
```c
struct sdshdr {
	char[] buf;
	long len;
	long free;
}
```

* buf : 使用字符数组存储字符串
* len: 有效字符个数 -- > 在Redis中获取字符创长度的时间复杂度为o(1)
* free:剩余空间 = buf.size() - len

对于append方法,大多数情况下都是追加一些简单的字符串,可以直接根据len,在buf尾部直接添加,不需要从头开始遍历到数组末尾,所以时间复杂度是 o(1)

但是在极少数场景下,需要追加的字符串长度太长了,需要进行扩容,而Redis中的扩容策略是直接扩两倍的,也就是大部分场景下扩容一次就可以完成字符串的拼接,不需要频繁的realloc,均摊下来时间复杂度就是o(1)


**注意**
append方法返回的数字,单位是字节;要注意对于中文场景下,具体的长度取决于终端使用的字符集编码

使用Redis存储汉字时,尝试获取返回的是 16进制编码的结果
![[Pasted image 20250928174316.png]]

如果想要显示中文,可以在启动Redis客户端时添加 --raw选项

![[Pasted image 20250928174408.png]]

# getrange
> getrange key start end : 获取 [start, end]之间的字符串  这里是左闭右闭

![[Pasted image 20250928175027.png]]



# setrange
> setrange key offset value : 从offset开始,,覆盖长度为value.size()长度的原有字符串

![[Pasted image 20250928175957.png]]

# strlen
> strlen key : 返回字符串的长度   return len  这里的单位,仍然是字节

![[Pasted image 20250928180319.png]]

* 如果key不存在,直接返回0
* 如果key中的value的数据类型不为string,报错
![[Pasted image 20250928180439.png]]


# string类型的编码方式
![[Pasted image 20250928181042.png]]

# 应用场景

## 缓存
![[Pasted image 20250930214838.png]]
* 1.为什么直接访问Redis :Redis存储的是热点数据, 数据存储在内存之中,比直接访问磁盘要快的多
* 2.为什么从MySQL中获取到数据之后 要执行 **write cache** : 暗含一个条件, 用户此时访问的数据是高频的,是经常需要进行访问的
* 随着时间的推移,Redis中的key不会越来越多吗: 存储数据的时候可以设置ttl,同时Redis中也有一套淘汰策略
[[代码演示Redis缓存]]

## 计数器
![[Pasted image 20251001090120.png]]为什么Redis的string适合做计数器呢?主要有以下几个优点
1. 轻量化 : 如果直接使用MySQL,大致的操作是 select - > update(+1),开销较大;而Redis的string天然支持对数字 += n的操作,性能更高
2. 天然线程安全:Redis所有命令的执行都是单线程的,而且incr这样的操作本身就是原子化的,不会出现线程 不安全问题
3. 高性能(内存存储):Redis将数据存储到内存之中,存储速度更快,响应时间更快;对于一些需要实时统计数据的场景很适用,比如统计视频的统计量,秒杀库存等等
4. 灵活过期时间设置 : 可以通过expire设置过期时间,ttl命令查看过期时间;比如需要统计当天视频播放量,就可以将过期时间设置为24h,一旦过期就会自动清空在Redis中存储的数据

# 共享会话
![[Pasted image 20251001093042.png]]
session是在服务器中存储的和用户信息有关的数据(比如登录状态),而在分布式系统中,对于用户的请求我们使用的是负载均衡的方式进行流量的分配,即按照一定的算法讲请求分配给不同的服务器

所以,同一个用户两次访问,可能每次访问的服务器是不同的,如果不做**会话共享**,就有可能出现一些问题(比如服务器A有用户的登录状态,服务器B没有,当用户访问服务器B时,就需要重新登录),基于上述问题,我们可以将会话单独提取出来,存储在Redis之中,起到一个**共享会话**的机制

优点:
1. 实现会话共享,无需在每个服务器中都保存session,减少服务器的压力
2. session其实也是一种**高频数据**,需要经常被访问,存储在Redis之中可以加快访问速度(相较于使用一个db作为共享会话)

# 手机验证码存储

```java
public String getCode(String phoneNumber)
{
	// 1.限制每隔60s才能获取一次验证码数据  nx : key不存在才设置
	// 2.此处使用Redis : 避免用户在短时间内大量请求发送验证码
	boolean flg = redis set ph 1 ex 60 nx 
	if(flg == false) return "";// 这个手机号已经设置过验证码了/当前手机号还没有过期
	
	// 当前手机号没有被设置验证码
	String code = generateRandomCode(ph);
	
	// 2.对于验证码本身来说也需要设置过期时间  一般是5min
	redis set ph code ex 3600
	
	return code
}
```