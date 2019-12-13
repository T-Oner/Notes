# 爬虫之 SSH 证书警告错误

## 错误信息：

```text
(Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')])")))
```

## 错误分析：

ssh证书是美国网景公司发放的一个安全认证证书，有了这个证书即可证明网站是安全的，但是认证是需要收费的，

所以一些网站就会自己仿造证书，这个时候浏览器就会给予警告，而我们爬虫就爬不到想要的信息

## 解决办法：

### 方式一：

加上一个参数：verify=证书路径，或verify=False

```python
#方法一<br>import  requests
from bs4 import BeautifulSoup

url = 'https://inv-veri.chinatax.gov.cn/'
req = requests.get(url,verify=False)
req.encoding = 'utf-8'
soup = BeautifulSoup(req.text,'lxml')
print(soup)
```

### 方式二：

```python
#注意用了这个就不能用requests了，得用urllib2.Request
import ssl
import urllib2

ssl._create_default_https_context = ssl._create_unverified_context
req = urllib2.Request('https://inv-veri.chinatax.gov.cn/')
data = urllib2.urlopen(req).read()
print(data)
```

### 方式三：

我们可以通过设置忽略警告的方式来屏蔽这个警告：

```python
import requests
from requests.packages import urllib3

urllib3.disable_warnings()
response = requests.get('https://www.12306.cn', verify=False)
print(response.status_code)
```

或者通过捕获警告到日志的方式忽略警告：

```python
import logging
import requests
logging.captureWarnings(True)
response = requests.get('https://www.12306.cn', verify=False)
print(response.status_code)
```

## 后续

发现关闭Charles后不用关闭校验也能请求通过

