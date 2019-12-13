# 解决requests-SSL: CERTIFICATE\_VERIFY\_FAILED\] 问题

在一些自签名的https请求前，大家往往就不知道该如何爬取数据了。

其实在requests请求中设置verify=False即可

```python
url='https://XXXXXX'
data='xxxxxx'
send\_data={'data':data}
res=requests.post(url,send\_data,verify=False).json()
```

