# 动态 IP 池



在爬虫过程中，很容易被封 ip，如果需要的数据量少，sleep 就完事了。但是如果需要大量的数据，sleep 就有点蛋疼了。 下面介绍一下如何设置动态 ip 池。

> 如果要测试代理是否成功, 请求 `http://icanhazip.com` 这个网站，他会返回你请求的 ip 值。

## request模块

```python
import requests

proxies = {
  "http": "http://10.10.1.10:3128",
  "https": "http://10.10.1.10:1080",
}

r=requests.get("http://icanhazip.com", proxies=proxies)
print r.text
```

也可以通过环境变量 HTTP\_PROXY 和 HTTPS\_PROXY 来配置代理

```python
export HTTP_PROXY="http://10.10.1.10:3128"
export HTTPS_PROXY="http://10.10.1.10:1080"
python
>>> import requests
>>> r=requests.get("http://icanhazip.com")
>>> print r.text
```

若你的代理需要使用 HTTP Basic Auth，可以这样 `http://user:password@host/`

```python
proxies = {
    "http": "http://user:pass@10.10.1.10:3128/",
}
```

## urllib 模块

urllib/urllib2使用代理比较麻烦, 需要先构建一个ProxyHandler的类, 随后将该类用于构建网页打开的opener的类, 再在request中安装该opener.

```python
proxy="http://112.25.41.136:80"
# Build ProxyHandler object by given proxy
proxy_support=urllib.request.ProxyHandler({'http':proxy})
# Build opener with ProxyHandler object
opener = urllib.request.build_opener(proxy_support)
# Install opener to request
urllib.request.install_opener(opener)
# Open url
r = urllib.request.urlopen('http://icanhazip.com',timeout = 1000)
```

