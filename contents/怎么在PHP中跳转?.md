# 怎么在PHP中跳转网址?

## question
使用PHP跳转到一个不同的页面是可行的吗?

用户访问`www.example.com/page.php`,但是我想要让他们跳转到`www.example.com/index.php`,我怎么做才可以不让用户浏览器刷新?可行吗?

这个甚至可以保护我的页面不被非法用户访问。
	
## answers
下面是已经存在的问题的总结,还有我自己的一点观点。

### 1.基本回答

你能够使用`header()`函数发送新的HTTP头部,但是必须发送给浏览器在任何HTML或者文本数据之前。(比如,在`<!DOCTYPE ...>`声明之前）

	header('Location: '.$newURL);
	
### 2.重要的细节
#### die()或者exit()

	header("Location: http://example.com/myOtherPage.php");
	die();
	
为什么你应该使用die()或者exit():[The Daily WTF](http://thedailywtf.com/articles/WellIntentioned-Destruction)

#### 绝对URL
URL必须是绝对的。看[RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.30)。但是多数情况下,相对路径也可以用。

#### 状态码
PHP的"Location"-头部仍然使用[HTTP 302](https://en.wikipedia.org/wiki/HTTP_302)跳转码,但是这不是一个你应该使用的状态码。你应该使用[301](https://en.wikipedia.org/wiki/HTTP_301)(永久跳转)或者[303](https://en.wikipedia.org/wiki/HTTP_303)(其他)。

注意:[W3C](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.4)提及303头部对很多HTTP/1.1之前的客户端是不兼容的。当前浏览器都是HTTP/1.1客户端。但是对于其他的爬虫和机器人客户端是不确定的。

### 3.文档
HTTP头部和PHP中的header()函数
* [PHP手册](http://www.php.net/manual/en/function.header.php)
* [维基百科](https://en.wikipedia.org/wiki/List_of_HTTP_headers#Responses)
* [W3C](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.30)

### 4.替代方案
你可以使用替代方法`http_redirect($url)`,这个方法需要安装[PECL包](http://pecl.php.net/package/pecl_http)

### 5.帮助函数
这个函数不会合并303状态码:

	function Redirect($url, $permanent = false)
	{
	    header('Location: ' . $url, true, $permanent ? 301 : 302);
	
	    exit();
	}

	Redirect('http://example.com/', false);
	
下面是更加灵活地方案:

	function redirect($url, $statusCode = 303)
	{
	   header('Location: ' . $url, true, $statusCode);
	   die();
	}

### 6.补救方案
提及的`header()`跳转仅仅工作在任何输出之前。通常会因为有输出而失败。然后你可以使用一个HTML头部补救：

	<meta http-equiv="refresh" content="0;url=finalpage.html">
	
或者一个JavaScript跳转.

	window.location.replace("http://example.com/");

### stackoverflow链接: 
* https://stackoverflow.com/questions/768431/how-to-make-a-redirect-in-php
* vote: 982