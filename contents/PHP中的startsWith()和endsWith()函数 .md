# PHP中的startsWith()和endsWith()函数 
## question
我怎么去写一个函数，能够接收一个字符串，返回另一个字符/字符串是否在它的开始或者结尾处?

比如:

	$str = '|apples}';
	
	echo startsWith($str, '|'); //Returns true
	echo endsWith($str, '}'); //Returns true
	
## answers

	function startsWith($haystack, $needle)
	{
	     $length = strlen($needle);
	     return (substr($haystack, 0, $length) === $needle);
	}
	
	function endsWith($haystack, $needle)
	{
	    $length = strlen($needle);
	
	    return $length === 0 || 
	    (substr($haystack, -$length) === $needle);
	}
如果你不想使用正则，可是使用上面的函数。

### stackoverflow链接: 
* https://stackoverflow.com/questions/834303/startswith-and-endswith-functions-in-php
* vote: 1212