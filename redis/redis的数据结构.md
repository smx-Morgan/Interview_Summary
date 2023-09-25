**redis 有哪些数据结构 分别用于什么场景**

String ： 储存结构体对象

hash ： 购物车登录用户ID号做为KEY，商品ID号为Field, 加入购物车数量为value

list ： 好友列表

set：无序集合 抽奖

zset 有序集合 排行榜 实现->跳跃链表

## 衍生问题

**redis的数据结构底层**

 **String**: int编码 ， embstr编码，raw编码

**Hash**: ziplist（压缩列表）：当Hash类型的元素比较少，且元素的大小比较小（小于64字节）时，Redis采用ziplist作为Hash类型的内部编码。 hashtable：当Hash类型的元素比较多，或者元素的大小比较大（大于64字节）时，Redis采用hashtable作为Hash类型的内部编码。

**List**: 双向链表和压缩列表

**Set**： intset 和 hashtable

**zset**： 跳跃链表
