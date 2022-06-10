# urllib使用

```python
#用来发送url请求
import urllib.request
#用来处理编码问题
import urllib.parse
```

## 常用方法

```python
#设置字符串常量
encoding = 'utf-8'
#设置url路径
url = 'http://www.baidu.com'
# urlopen()获取包含页面源代码, 返回状态码等信息
response = urllib.request.urlopen(url=url)
# read()获取二进制的页面源代码
content = response.read()
# decode()将二进制数据按照utf-8的编码方式转化为字符串
text = content.decode(encoding)#text为网页源代码的字符串形式
# urlretrieve()下载
filename = 'baidu.html' #文件的保存路径
urllib.request.urlretrieve(url=url,filename=filepath)
```

## url的构成

```python
https://www.google.com.hk/search?q=周杰伦
#周杰伦的unicode编码,三字节表示一个汉字
https://www.google.com.hk/search?q=%E5%91%A8%E6%9D%B0%E4%BC%A6
```

1. 协议
   + http
   + https
2. 主机(域名)
   + www.google.com.hk
   + www.baidu.com
   + www.jd.com
3. 路径
   + search
4. 参数**(问号后面的部分)**
   + q=周杰伦