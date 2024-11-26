
# MockHTTP


MockHTTP 可以将现有HTTP转换成GET请求，设置cron定时调用，自定义代码去格式化数据，通知到你的邮箱里。
比如每天早上时自动获取博客园文章列表，天气下雨时通知，各类签到等


## Request


### 创建一个请求


可以使用VSCode或VS，创建一个.http文件



```
POST https://www.wdora.com/mockhttp/request/xxx

GET https://github.com/ HTTP/1.1
Host: www.cnblogs.com
Connection: keep-alive
Cache-Control: max-age=0
sec-ch-ua: "Microsoft Edge";v="131", "Chromium";v="131", "Not_A Brand";v="24"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36 Edg/131.0.0.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br, zstd
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6

```

### 使用请求


返回对象：


GET [https://www.wdora.com/mockhttp/request/xxx](https://github.com)


响应报文原始格式：


GET [https://www.wdora.com/mockhttp/request/xxx?raw\=true](https://github.com):[悠兔机场](https://xinnongbo.com)


### 定时调度


每天早上9点收到1个邮件,内容为请求返回的对象Body



```
POST https://www.wdora.com/mockhttp/sched/yyy
Content-Type: application/json

{"cron": "0 9 * * *", "requestName": "xxx", "notifyEmail": "xx@yy.com" }

```

原始的对象Body 是一个html, 不易读，可以自定义代码格式化(基于.NET 9 BCL, 支持XPath, JToken)



```
POST https://www.wdora.com/mockhttp/sched/yyy
Content-Type: application/json

{"cron": "0 9 * * *", "requestName": "xxx", "notifyEmail": "xx@yy.com", "parseCode": "x => string.Join(';', XPath.Parse(x.Body, \"//article//div/a\"))" }

```

* parseCode 会被编译成 Func，返回null，则不通知
* XPath.Parse 是基于 HtmlAgilityPack 二次封装的方法(更方便调用，也支持原有的 HtmlAgilityPack 的方法)
* JToken.Parse 是基于 Newtonsoft.Json 自带的方法
* 你也可以使用.NET 自带的Regex 等能力去定制化返回
* 由于本应用容器化限制了内存，所以你的定制化代码应尽量减少内存占用


会收到一封邮件
![](https://img2024.cnblogs.com/blog/677578/202411/677578-20241125190733738-1891307732.png)


