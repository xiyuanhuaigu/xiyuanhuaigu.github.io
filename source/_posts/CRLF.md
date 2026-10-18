---
title: "CRLF"
date: 2022-05-16 21:32:47
---
## 简介：

CRLF的意思

就是回车(CR, ASCII 13, \r) 换行(LF, ASCII 10, \n)。

换行在有的[ASCII](https://baike.baidu.com/item/ASCII)码表也用newline（简nl）来进行表示,这里的lf是line feed的概念，意思是一样的。

这两个ACSII字符不会在屏幕有任何输出，但在Windows中广泛使用来标识一行的结束。而在Linux/UNIX系统中只有换行符。

### CRLF命令

| 代码 | ASCII码 | 符号 | 概念 |
| --- | --- | --- | --- |
| CR | 13 | \r | 光标移到行首 |
| LF | 10 | \n | 光标垂直移到下行 |
| CRLF |  | %0d%0a |  |

注意:但是不同的操作系统行的结束符是不一样的,所以同一文件在不同操作系统中打开，内容格式可能会出现差异，这是行结束符不一致导致的。

- Windows：使用CRLF表示行的结束
- Linux/Unix：使用LF表示行的结束
- MacOS：早期使用CR表示，现在好像也用LF表示行的结束

在HTTP规范中，行应该使用CRLF来结束。首部与主体由两个CRLF分隔，浏览器根据这两个CRLF来获取HTTP内容并显示。

### CRLF注入

就是说黑客能够将CRLF命令注入到系统中。它不是系统或[服务器软件](https://baike.baidu.com/item/%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%BD%AF%E4%BB%B6)的漏洞，而是网站应用开发时，有些开发者没有意识到此类攻击存在的可能而造成的。

针对这个漏洞黑客能够做什么？

就算黑客发现网站存在CRLF注入，他们仍然受到应用结构和这个缺陷的严重程度的限制。

对有些站点它将非常严重，而有些站点它只是很小的bug。

HTTP Header CRLF Injection

许多[网络协议](https://baike.baidu.com/item/%E7%BD%91%E7%BB%9C%E5%8D%8F%E8%AE%AE)，包括HTTP也使用CRLF来表示每一行的结束。这就意味着用户可以通过CRLF注入自定义HTTP header，导致用户可以不经过[应用层](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E5%B1%82)直接与Server对话。

HTTP header的定义就是基于这样的”Key: Value”的结构，用CRLF命令表示一行的结尾。

“Location:”头用来表示重定向的URL地址，”Set-Cookie:”头用来设置cookies。

如果用户的输入经过验证，其中存在CRLF的[字符](https://baike.baidu.com/item/%E5%AD%97%E7%AC%A6)就可以被用来达到欺骗的目的。

#### CRLF漏洞原理

描述：CRLF注入漏洞的本质和XSS有点相似，攻击者将恶意数据发送给易受攻击的Web应用程序，Web应用程序将恶意数据输出在HTTP响应头中。（XSS一般输出在主体中） 所以CRLF注入漏洞的检测也和XSS漏洞的检测差不多。通过修改HTTP参数或URL，注入恶意的CRLF，查看构造的恶意数据是否在响应头中输出。

*原理1：找到输入点，构造恶意的CRLF字符* 描述:header可控的请求我们就可以尝试进行CRLF注入作为演示

```plaintext
#现在HTTP标头中的每一行都由CRLF分隔（如前所述，这是不可打印的ASCII字符）。
#Request [CRLF]
GET /test/demo.php?url=https://weiyigeek.github.io [CRLF]
Host: 127.0.0.1 [CRLF]
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:69.0) Gecko/20100101 Firefox/69.0 [CRLF]
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8 [CRLF]
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2  [CRLF]
Accept-Encoding: gzip, deflate [CRLF]

#Response
HTTP/1.1 200 OK [CRLF]
Connection: keep-alive [CRLF]
Content-Encoding: deflate [CRLF]
Locations=https://weiyigeek.github.io [CRLF]
```

抓包在请求行的url参数中加入特殊构造的CRLF字符,查看恶意数据是否在响应头中多了个Set-Cookie字段，如果证实了该系统存在CRLF注入漏洞就可以进行下一步；

![](/2022/05/16/CRLF/1.png)

具体请看 <https://cloud.tencent.com/developer/article/1728657>
