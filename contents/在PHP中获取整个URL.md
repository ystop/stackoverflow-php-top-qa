# 在PHP中获取整个URL

## question
我使用这个代码获取整个URL:

	$actual_link = 'http://'.$_SERVER['HTTP_HOST'].$_SERVER['PHP_SELF'];

问题是我在.htaccess隐藏了一些,所以我想要看的URL不总是文件的实际位置。

我需要获取的是URL,一点不多也不少，整个URL。

我要知道的是出现在浏览器的地址栏上的,而不是实际的在服务器上面的文件位置。
	
## answers
看看`$_SERVER['REQUEST_URI']`,例如:

	$actual_link = "http://$_SERVER[HTTP_HOST]$_SERVER[REQUEST_URI]";
	
(注意这个双引号的语法是[非常恰当必须的](http://php.net/language.types.string#language.types.string.parsing.simple))

如果你不仅要支持HTTPS，还要HTTPS，你可以这么写:

	$actual_link = (isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] === 'on' ? "https" : "http") . "://	$_SERVER[HTTP_HOST]$_SERVER[REQUEST_URI]"

编辑者注:使用这个代码是有安全隐患的。客户端可以设置HTTP_HOST和REQUEST_URI为他想设置的任何值。

### stackoverflow链接: 
* https://stackoverflow.com/questions/6768793/get-the-full-url-in-php
* vote: 979