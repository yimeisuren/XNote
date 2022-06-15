# Ajax

## XML和HTML

XML和HTML类似, 不同之处在于HTML中的是预定义标签, 而XML均为自定义标签.

曾经使用过XML作为数据交换的格式, 现在被Json替代

## HTTP协议

HTTP协议, 即超文本传输协议, 规定了浏览器和服务器之间互相通信的规则.

### 请求报文

+ 请求行

+ 请求头Request Headers

  ```http
  Request Headers
  Accept: */*
  Accept-Encoding: identity
  Accept-Language: zh,en-US;q=0.9,en;q=0.8,zh-CN;q=0.7
  Connection: keep-alive
  Host: upos-sz-mirrorcos.bilivideo.com
  If-Range: "0aa91927238be9e9c9cbe7ad617e822a"
  Origin: https://www.bilibili.com
  Range: bytes=6772636-6852773
  Referer: https://www.bilibili.com/video/BV1WC4y1b78y?p=5&spm_id_from=pageDriver&vd_source=026188caf92d306f604935144e3628fe
  sec-ch-ua: " Not A;Brand";v="99", "Chromium";v="102", "Google Chrome";v="102"
  sec-ch-ua-mobile: ?0
  sec-ch-ua-platform: "Windows"
  Sec-Fetch-Dest: empty
  Sec-Fetch-Mode: cors
  Sec-Fetch-Site: cross-site
  User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36
  ```

+ 请求体

  get请求会将请求参数中的内容拼接到URL中

  post请求会封装到Payload中

  ```http
  Query String Parameters
  e:ig8euxZM2rNcNbdlhoNvNC8BqJIzNbfqXBvEqxTEto8BTrNvN0GvT90W5JZMkX_YN0MvXg8gNEV4NC8xNEV4N03eN0B5tZlqNxTEto8BTrNvNeZVuJ10Kj_g2UB02J0mN0B5tZlqNCNEto8BTrNvNC7MTX502C8f2jmMQJ6mqF2fka1mqx6gqj0eN0B599M=
  uipk: 5
  nbs: 1
  deadline: 1655175495
  gen: playurlv2
  os: cosbv
  oi: 0
  trid: e387a180914748ffaf51eef10917d4c0u
  mid: 355064400
  platform: pc
  upsig: c20931b2c7fe5a5ca823790fddd0b3d5
  uparams: e,uipk,nbs,deadline,gen,os,oi,trid,mid,platform
  bvc: vod
  nettype: 0
  orderid: 0,2
  agrr: 0
  bw: 23159
  logo: 80000000
  ```

### 响应报文

## Ajax特点

作用: 异步加载

<u>优势:</u> 

+ 不用刷新原有页面就可以获取数据
+ 允许根据用户事件来更新部分网页内容

<u>缺点:</u>

+ 没有浏览历史, 不能回退
+ 存在跨域问题
+ SEO不友好(对爬虫不友好)

用途:

+ 懒加载
+ 输入校验
+ 搜索引擎

## Ajax使用

