# 你是怎么在php中使用bcrypt哈希密码的?
## question
我不时听说“在php中使用bcrypt存储密码,bcrypt规则"的建议。

但是`bcrypt`是什么?PHP没有提供任何函数，维基百科含糊地说是一个文件加密工具,web上的搜索也只是说明它是Blowfish在不同语言的一些实现。现在, Blowfish也可以通过`mcrypt`在PHP中使用，但是它是怎么帮助保存密码的呢? Blowfish是一个通用的加密，它工作在两方。如果它能被加密,它也可以被解密。密码需要一个单向的hash函数。

这怎么解释呢?

## answers
`bcrpty`是一个hash算法,它可以根据硬件情况扩展(通过配置轮数)。它的速度慢和多次的循环确保攻击者必须花费大量的金钱和硬件才可以破解你的密码。增加一个每个密码的[salt](https://en.wikipedia.org/wiki/Salt_%28cryptography%29)(`bcrypt`需要`salts`)，除非惊人的资金和硬件成本,基本可以确保一个攻击者几乎是不可能破解开的。

`bcrypt`使用`Eksblowfish`算法hash密码。虽然Eksblowfish和Blowfish的加密十分相似,关键在于Eksblowfish确保了任何后面的状态都同时依赖于salt和key(用户密码),而且不知道这2个的话，没有任何状态是可以被提前计算的。**由于key是不同的,bcrypt是一个单项hash算法**。如果你不提前知道salt,轮数和key(密码),那就不能够恢复原始密码的。[[Source](http://www.usenix.org/events/usenix99/provos/provos_html/node4.html)]

### 使用bcrypt:

### 使用PHP >+ 5.5-DEV

密码hash函数[已经在PHP>=5.5里内置](http://php.net/password_hash)。你能够使用`password_hash()`对任何密码去生成`bcrypt` hash:

	<?php
	// Usage 1:
	echo password_hash('rasmuslerdorf', PASSWORD_DEFAULT)."\n";
	// $2y$10$xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
	// For example:
	// $2y$10$.vGA1O9wmRjrwAVXD98HNOgsNpDczlqm3Jq7KnEd1rVAGv3Fykk1a
	
	// Usage 2:
	$options = [
	  'cost' => 11
	];
	echo password_hash('rasmuslerdorf', PASSWORD_BCRYPT, $options)."\n";
	// $2y$11$6DP.V0nO7YI3iSki4qog6OQI5eiO6Jnjsqg7vdnb.JgGIsxniOn4C

通过已经存在的hash值，验证一个用户提供的密码，你可以这么使用`password_verify()`:

	<?php
	// See the password_hash() example to see where this came from.
	$hash = '$2y$07$BCryptRequires22Chrcte/VlQH0piJtjXl.0t1XkA8pw9dMXTpOq';
	
	if (password_verify('rasmuslerdorf', $hash)) {
	    echo 'Password is valid!';
	} else {
	    echo 'Invalid password.';
	}

### Using PHP >= 5.3.7, < 5.5-DEV (RedHat PHP >= 5.3.3也可以)

在[github](https://en.wikipedia.org/wiki/GitHub)里有一个[兼容库](https://github.com/ircmaxell/password_compat)，基于高版本php c函数源码,提供了相同的函数。一旦安装好兼容库,使用方法和上面一样(如果你还在5.3.x分支上,记得去掉数组的简写).

### 使用PHP5.3.7(废弃的)
你你能够使用`crypt()`函数对于输入的字符串去生成bcrypt哈希串。这个类能够自动地生成salts而且验证已经的存在的hash。如果你使用的PHP版本大于等于5.3.7,是非常推荐你使用内置的函数或者是兼容库。这个替代品仅仅提供给历史代码。

	class Bcrypt{
	  private $rounds;
	
	  public function __construct($rounds = 12) {
	    if (CRYPT_BLOWFISH != 1) {
	      throw new Exception("bcrypt not supported in this installation. See http://php.net/crypt");
	    }
	
	    $this->rounds = $rounds;
	  }
	
	  public function hash($input){
	    $hash = crypt($input, $this->getSalt());
	
	    if (strlen($hash) > 13)
	      return $hash;
	
	    return false;
	  }
	
	  public function verify($input, $existingHash){
	    $hash = crypt($input, $existingHash);
	
	    return $hash === $existingHash;
	  }
	
	  private function getSalt(){
	    $salt = sprintf('$2a$%02d$', $this->rounds);
	
	    $bytes = $this->getRandomBytes(16);
	
	    $salt .= $this->encodeBytes($bytes);
	
	    return $salt;
	  }
	
	  private $randomState;
	  private function getRandomBytes($count){
	    $bytes = '';
	
	    if (function_exists('openssl_random_pseudo_bytes') &&
	        (strtoupper(substr(PHP_OS, 0, 3)) !== 'WIN')) { // OpenSSL is slow on Windows
	      $bytes = openssl_random_pseudo_bytes($count);
	    }
	
	    if ($bytes === '' && is_readable('/dev/urandom') &&
	       ($hRand = @fopen('/dev/urandom', 'rb')) !== FALSE) {
	      $bytes = fread($hRand, $count);
	      fclose($hRand);
	    }
	
	    if (strlen($bytes) < $count) {
	      $bytes = '';
	
	      if ($this->randomState === null) {
	        $this->randomState = microtime();
	        if (function_exists('getmypid')) {
	          $this->randomState .= getmypid();
	        }
	      }
	
	      for ($i = 0; $i < $count; $i += 16) {
	        $this->randomState = md5(microtime() . $this->randomState);
	
	        if (PHP_VERSION >= '5') {
	          $bytes .= md5($this->randomState, true);
	        } else {
	          $bytes .= pack('H*', md5($this->randomState));
	        }
	      }
	
	      $bytes = substr($bytes, 0, $count);
	    }
	
	    return $bytes;
	  }
	
	  private function encodeBytes($input){
	    // The following is code from the PHP Password Hashing Framework
	    $itoa64 = './ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
	
	    $output = '';
	    $i = 0;
	    do {
	      $c1 = ord($input[$i++]);
	      $output .= $itoa64[$c1 >> 2];
	      $c1 = ($c1 & 0x03) << 4;
	      if ($i >= 16) {
	        $output .= $itoa64[$c1];
	        break;
	      }
	
	      $c2 = ord($input[$i++]);
	      $c1 |= $c2 >> 4;
	      $output .= $itoa64[$c1];
	      $c1 = ($c2 & 0x0f) << 2;
	
	      $c2 = ord($input[$i++]);
	      $c1 |= $c2 >> 6;
	      $output .= $itoa64[$c1];
	      $output .= $itoa64[$c2 & 0x3f];
	    } while (true);
	
	    return $output;
	  }
	}

你应该像这样使用代码:

	$bcrypt = new Bcrypt(15);
	
	$hash = $bcrypt->hash('password');
	$isGood = $bcrypt->verify('password', $hash);

还有个可以替代的，你还可以使用  [Portable PHP Hashing Framework.](http://www.openwall.com/phpass/)

### stackoverflow链接: 
* https://stackoverflow.com/questions/4795385/how-do-you-use-bcrypt-for-hashing-passwords-in-php
* vote: 1141