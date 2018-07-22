# 什么时候使用self而不是$this?

## question
在php5,`self`和`$this`的区别是什么？
他们各自的使用场景都是什么?

## answers
简短回答:

> 使用`$this`代表是当前对象，使用`self`代表当前类。换句话说，使用`$this->member`是对非静态成员,使用`self::$memeber`是对于静态成员。

全部回答:

下面是一个`$this`和`self`对于非静态和静态成员变量用法的**正确**示例:

	<?php
	class X {
	    private $non_static_member = 1;
	    private static $static_member = 2;
	
	    function __construct() {
	        echo $this->non_static_member . ' '
	           . self::$static_member;
	    }
	}
	
	new X();
	?>

下面是一个`$this`和`self`对于非静态和静态成员变量用法的**错误**示例:

	<?php
	class X {
	    private $non_static_member = 1;
	    private static $static_member = 2;
	
	    function __construct() {
	        echo self::$non_static_member . ' '
	           . $this->static_member;
	    }
	}
	
	new X();
	?>

下面是一个使用`$this`成员函数的**多态性**示例:

	<?php
	class X {
	    function foo() {
	        echo 'X::foo()';
	    }
	
	    function bar() {
	        $this->foo();
	    }
	}
	
	class Y extends X {
	    function foo() {
	        echo 'Y::foo()';
	    }
	}
	
	$x = new Y();
	$x->bar();
	?>
	
下面是一个使用`$this`成员函数**非多态性**示例:
	
	<?php
	class X {
	    function foo() {
	        echo 'X::foo()';
	    }
	
	    function bar() {
	        self::foo();
	    }
	}
	
	class Y extends X {
	    function foo() {
	        echo 'Y::foo()';
	    }
	}
	
	$x = new Y();
	$x->bar();
	?>
	
> 	原因是`$this->foo()`调用当前对象的`foo()`成员函数。如果当前对象的`类型X`,它会调用`X::foo()`。如果当前对象是`类型Y`,它调用`Y::foo()`。但是如果换成是`self::foo()`,那么`X::foo()`会被调用。


From [http://www.phpbuilder.com/board/showthread.php?t=10354489](http://www.phpbuilder.com/board/showthread.php?t=10354489):

By [http://board.phpbuilder.com/member.php?145249-laserlight](http://www.phpbuilder.com/board/showthread.php?t=10354489)

### stackoverflow链接: 
* https://stackoverflow.com/questions/151969/when-to-use-self-over-this
* vote: 1501