# 为什么不应该在php中使用mysql_*的函数
## question
我不应该使用mysql_*的函数的技术原因是什么呢?

(比如`mysql_query()`,`mysql_connect()` 和 `mysql_real_escape_string()`)?

即便它们是正常工作在我的网站里，为什么我仍然应该去用其他的?

如果它们在我的网站已经不能够正常运行，为什么我会得到下面的这样的错误

> Warning: mysql_connect(): No such file or directory


## answers
MySQL扩展:

* 已经不再活跃地发展了
* 官方在PHP5.5[不建议使用了](https://wiki.php.net/rfc/mysql_deprecation)(发布于2013年6月)
* 在PHP7.0 [全部被移除了](https://wiki.php.net/rfc/remove_deprecated_functionality_in_php7#extmysql)(发布于2015年12月）
	* 这意味着在2018年12月31日，它将不会出现在再任何支持中的PHP版本里。当前，它仅仅获得安全更新。
* 缺少面向对象接口
* 不支持:
	* 非阻塞,异步查询
	* [预处理语句](http://php.net/manual/en/mysqli.quickstart.prepared-statements.php)或者参数化查询
	* 存储过程
	* 多语句
	* 事务
	* 新的密码认证方法(MySQL5.6的默认要求，5.7的强制要求)
	* MySQL5.1中的所有功能点
* 由于它是被废弃的,使用它使你的代码未来的支持度更差。
* 缺少预处理语句的支持是特别重要的，因为预处理语句提供了一个更加清晰地，更少错误倾向地去转义和引用外部的数据，而不是通过调用一个单独的函数手动转义。
* 看看这篇 [the comparison of SQL extensions](http://php.net/manual/en/mysqlinfo.api.choosing.php).

### stackoverflow链接: 
* https://stackoverflow.com/questions/12859942/why-shouldnt-i-use-mysql-functions-in-php
* vote:1789