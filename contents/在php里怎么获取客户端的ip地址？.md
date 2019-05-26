# 在php里怎么获取客户端的ip地址？
## question
我怎么在php里获取客户端的ip?

我希望去通过ip地址记录用户的访问日志。

## answers
无论如何，不要相信任何客户端的数据。$_SERVER['REMOTE_ADDR']包含了连接对端的真实ip地址。这个是你可以用的最可靠的值。

但是，他们有可能是在一个代理服务器后面，这个代理也许会设置$_SERVER['HTTP_X_FORWARDED_FOR'],但是这个是值是很容易伪造的。比如，没有代理也可以设置，或者这个ip地址是一个内网的ip。

这意味着，你可以去保存$_SERVER['HTTP_X_FORWARD_FOR'],确保你也已经保存了$_SERVER['REMOTE_ADDR']的值。例如，保存这2个值在不同的数据库字段。

如果你想要保存ip地址在数据库里面作为一个string字段，确保你至少留45个字节。IPv6已经出现，他们地址比IPv4要长。

(注意IPv6通常使用39个字符，但是也有一个说明IPv6 notation for IPv4 addresses，说明了全长可以到45个字节。所以，如果你想要用39个字节，那么忘记吧，使用45个）


### stackoverflow链接:
* https://stackoverflow.com/questions/3003145/how-to-get-the-client-ip-address-in-php
* vote数:1223