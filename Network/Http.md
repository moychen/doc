# HTTP协议

## HTTP/2

### 报文格式



### 特性

## 状态码

```c++
switch (status_code)
    {
        case 100: return View("Continue");
        case 101: return View("Switching Protocols");
        case 103: return View("Early Hints");
        case 200: return View("OK");
        case 201: return View("Created");
        case 202: return View("Accepted");
        case 203: return View("Non-Authoritative Information");
        case 204: return View("No Content");
        case 205: return View("Reset Content");
        case 206: return View("Partial Content");
        case 300: return View("Multiple Choices");
        case 301: return View("Moved Permanently");
        case 302: return View("Found");
        case 303: return View("See Other");
        case 304: return View("Not Modified");
        case 305: return View("Use Proxy");
        // case 306: return View("(Unused)");
        case 307: return View("Temporary Redirect");
        case 308: return View("Permanent Redirect");
        case 400: return View("Bad Request");
        case 401: return View("Unauthorized");
        case 402: return View("Payment Required");
        case 403: return View("Forbidden");
        case 404: return View("Not Found");
        case 405: return View("Method Not Allowed");
        case 406: return View("Not Acceptable");
        case 407: return View("Proxy Authentication Required");
        case 408: return View("Request Timeout");
        case 409: return View("Conflict");
        case 410: return View("Gone");
        case 411: return View("Length Required");
        case 412: return View("Precondition Failed");
        case 413: return View("Payload Too Large");
        case 414: return View("URI Too Long");
        case 415: return View("Unsupported Media Type");
        case 416: return View("Requested Range Not Satisfiable");
        case 417: return View("Expectation Failed");
        case 421: return View("Misdirected Request");
        case 425: return View("Too Early"); // https://tools.ietf.org/html/rfc8470
        case 426: return View("Upgrade Required");
        case 428: return View("Precondition Required");
        case 429: return View("Too Many Requests");
        case 431: return View("Request Header Fields Too Large");
        case 451: return View("Unavailable For Legal Reasons");
        case 500: return View("Internal Server Error");
        case 501: return View("Not Implemented");
        case 502: return View("Bad Gateway");
        case 503: return View("Service Unavailable");
        case 504: return View("Gateway Timeout");
        case 505: return View("HTTP Version Not Supported");
        case 511: return View("Network Authentication Required");
        default:  return View("");
    }
```



## 1xx信息性

100继续

[101交换协议](https://www.restapitutorial.com/httpstatuscodes.html#)

[102处理（WebDAV）](https://www.restapitutorial.com/httpstatuscodes.html#)

## 2xx成功

 200 OK

 创建了201

202接受

203非权威信息

 204没有内容

205重设内容

206部分内容

207种多状态（WebDAV）

208已报告（WebDAV）

使用226 IM

 

## 3xx重定向

300种选择

301永久移动

找到302个

303查看其他

 304未修改

305使用代理

306（未使用）

307临时重定向

308永久重定向（实验性）

## [4xx客户端错误](https://www.restapitutorial.com/httpstatuscodes.html#)

 400错误的要求

 401未经授权

402需要付款

 403禁止

 找不到404

405方法不允许

406不可接受

要求407代理身份验证

408请求超时

 409冲突

410去了

411所需长度

412前提条件失败

413请求实体太大

414请求URI太长

415不支持的媒体类型

416请求的范围不满足

417期望失败

418我是茶壶（RFC 2324）

420增强镇静（Twitter）

422无法处理的实体（WebDAV）

423已锁定（WebDAV）

424依存关系失败（WebDAV）

425为WebDAV保留

426需要升级

428需要先决条件

429请求过多

431请求标头字段太大

444无反应（Nginx）

449重试（Microsoft）

450被Windows家长控制（Microsoft）阻止

451由于法律原因不可用

499个客户端关闭的请求（Nginx）

 

## [5xx服务器错误](https://www.restapitutorial.com/httpstatuscodes.html#)

 500内部服务器错误

501未实施

502错误的网关

503服务不可用

504网关超时

不支持505 HTTP版本

506式样还可以协商（实验性）

507存储空间不足（WebDAV）

508检测到循环（WebDAV）

509超出带宽限制（Apache）

510未扩展

511需要网络身份验证