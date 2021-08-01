# BJDCTF 2020

## Easy MD5

> *2021/07/06*

### 题目

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

### payload

```
/?password=ffifdyop
/?a=s878926199a&b=s155964671a
/?param1[]=1&param2[]=2
```

