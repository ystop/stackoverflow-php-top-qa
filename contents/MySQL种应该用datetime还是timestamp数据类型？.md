# MySQL中应该用datetime还是timestamp数据类型？
## question
你会推荐用datetime还是timestamp字段? 为什么?(使用MySQL)

我使用PHP作为服务端。

## answers
时间戳在MySQL中通常被使用于追踪记录改变,而且每次记录改变会被经常更新。 如果你想要存储一个特定的值,你应该使用一个datetime字段。
如果你打算在用UXIN时间戳或者一个MySQL原生的时间字段中选择一个,去使用原生的格式吧。你能够在MySQL内部做计算, 像`("SELECT DATE_ADD(my_datetime, INTERVAL 1 DAY)") ` , 而且如果你想在php里操作，当你查询数据的时候，转换成UNIX时间戳的数据格式也很简单 `("SELECT UNIX_TIMESTAMP(my_datetime)")` 。


### stackoverflow链接: 
* https://stackoverflow.com/questions/3737139/reference-what-does-this-symbol-mean-in-php
* vote数:1546