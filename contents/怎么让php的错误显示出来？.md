# 怎么让php的错误显示出来？
## question
我已经验证了我的php.ini文件，显示错误已经设置好了，而且设置的是E_ALL。我已经重启了我的Apache web服务器。

我甚至已经在我的脚本最前面写了下面几行，但是它甚至不可以捕获到语法错误。比如，我声明一个变量'$',但是我没有写';'结束符。但是我所有的脚本都显示一个空白页当这些错误发生时，但是我想看到浏览器显示出来错误。

	error_reporting(E_ALL);
	ini_set('display_errors', 1);
我接下来该怎么办？

## answers
以下代码一直是我在用的:

	ini_set('display_errors', 1);
	ini_set('display_startup_errors', 1);
	error_reporting(E_ALL);

但是，这不能让php显示出来语法错误，唯一的办法就是修改php.ini文件里的这行:
display_errors = on


### stackoverflow链接:
* https://stackoverflow.com/questions/1053424/how-do-i-get-php-errors-to-display
* vote数:2883