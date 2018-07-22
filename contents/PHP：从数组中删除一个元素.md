# PHP：从数组中删除一个元素
## question
在PHP中，有没有简单的方式从数组中删除一个元素，使`foreach ($array)` 不再包含这个元素？

我以为设置成`null`可以实现这个，但是很明显没有生效。

## answers
有不同的方式删除一个元素，在一些特定的场景中,这些办法更有用。

### 删除一个数组元素
如果你只是想删除一个数组元素，你能使用[unset()](https://secure.php.net/manual/en/function.unset.php)或者[array_splice()](https://secure.php.net/manual/en/function.array-splice.php)。

如果你只有值，但是不知道key,你可以使用array_search()获取key。

#### [unset()](https://secure.php.net/manual/en/function.unset.php) 方法
注意当你使用[unset()](https://secure.php.net/manual/en/function.unset.php)的时候，数组的key不会改变/重新索引。
如果你想要重新索引keys,你可以使用[array_values()](http://php.net/manual/en/function.array-values.php)在unset()之后,
array_values()可以把所有key转成从0开始的数字枚举.

Code

	<?php
	
	    $array = array(0 => "a", 1 => "b", 2 => "c");
	    unset($array[1]);
	               //↑ Key which you want to delete
	
	?>
	
Output

	Array (
	    [0] => a
	    [2] => c
	)
	
#### [array_splice()](https://secure.php.net/manual/en/function.array-splice.php) 方法
相对于[array_values()](http://php.net/manual/en/function.array-values.php)将会改变所有的键名变成数字，如果你使用[array_splice()](https://secure.php.net/manual/en/function.array-splice.php)，键名将会自动重新索引，关联数组的键名不会改变。

另外，array_splice()需要的是偏移数，而不是键!看第二个参数。

Code

	<?php
	
	    $array = array(0 => "a", 1 => "b", 2 => "c");
	    array_splice($array, 1, 1);
	                       //↑ Offset which you want to delete
	
	?>

Output

	Array (
	    [0] => a
	    [1] => c
	)

array_splice()和unset()一样，通过引用获取数组。这意味着你不可以把这些函数的返回值赋值给一个数组。

### 删除多个数组元素
如果你想要删除多个数组元素，而且不想多次用`unset()`或者`array_splice()`,你可以使用`array_diff()`或者`array_diff_key()`函数，取决于你将要删除的元素是知道值还是键。

#### [array_diff()](http://php.net/manual/en/function.array-diff.php) 方法
如果你知道你要删除的元素的值,你可以使用`array_diff()`。和之前的unset()一样，它不能够改变/重新索引数组的key。

Code

	<?php
	
	    $array = array(0 => "a", 1 => "b", 2 => "c");
	    $array = array_diff($array, ["a", "c"]);
	                              //└────────┘→ Array values which you want to delete
	
	?>
Output

	Array (
	    [1] => b
	)


#### [array_diff_key()](http://php.net/manual/en/function.array-diff-key.php) 方法
如果你知道你要删除的元素的键名,你可以使用array_diff_key()。你必须确认你在第二个参数给的数组有意义的部分是键名,而不是值。否则，你需要通过array_flip()交换数组的键和值。这个函数也不会改变/重新索引。

Code

	<?php
	
	    $array = array(0 => "a", 1 => "b", 2 => "c");
	    $array = array_diff_key($array, [0 => "xy", "2" => "xy"]);
	                                   //↑           ↑ Array keys which you want to delete
	?>
	
Output

	Array (
    	[1] => b
	)
另外如果你要使用`unset()`或者`array_slice()`删除相同值的多个元素，你可以使用[array_keys()](http://php.net/manual/en/function.array-keys.php)获取所有的相同值的key，然后删除所有的元素。

### stackoverflow链接: 
* https://stackoverflow.com/questions/369602/php-delete-an-element-from-an-array
* vote:2202