# clue 监控平台

## 常见的几种种监控项:
### 1、异常事件
javascript报错

### 2、接口异常
通过重写 JavaScript 原生的 XMLHttpRequest 和 fetch 两个 API，实现接口出错功能的自动监控，同时支持传入一个 evaluate 方法，自定义判断接口返回结果是否异常（适用于后端将异常包裹为 JSON 并统一返回 HTTP 200 的情况）。

### 3、性能监控
分析页面加载时性能，以及资源下载时长，分析慢资源。

### 4、资源错误监控
对获取前端静态资源（script、image、css等）失败进行监控
多见于网络错误，比如个别用户的 DNS 劫持，特定域名的解析失败，资源不存在等

### 5、自定义监控 [地址](https://yuque.antfin-inc.com/clue/help/wxyrb6)
针对特定业务场景的监控

*Tracker 是 Clue 底层使用的 JS 打点库* [文档地址](https://yuque.antfin-inc.com/clue/help/wdylmk)

----

## 项目

### 1.boss-vue

> pid: maizuo-boss
> name: 麦座前端boss
> [应用地址](https://clue.alibaba-inc.com/dashboard?pid=maizuo-boss)
> [监控项地址](https://clue.alibaba-inc.com/project?pid=maizuo-boss&tab=custom)

#### 1-1.监控内容
1、接口报错（status = 200, 业务报错）
http code 200
2、登录滑块错误日志
3、打印错误日志
4、高德地图错误日志
5、支付失败错误日志
6、系统开小差 or innerCode高优错误
innerCode 后两位判断
7、打点方法自身错误

### 2.boss-react

> pid: boss-react
> name: 麦座-BOSS-React
> [应用地址](https://clue.alibaba-inc.com/dashboard?pid=boss-react)
> [监控项地址](https://clue.alibaba-inc.com/project?pid=boss-react&tab=custom)

#### 2-1.监控内容
1、接口报错（status = 200, 业务报错）
---【5个占位】
2、打点方法自身错误


### 3.pc

> pid: maizuo-pcsite
> name: 麦座前端pc站
> [应用地址](https://clue.alibaba-inc.com/dashboard?pid=maizuo-pcsite)
> [监控项地址](https://clue.alibaba-inc.com/project?pid=maizuo-pcsite&tab=custom)

#### 3-1.监控内容
1、接口报错（status = 200, 业务报错）
2、登录滑块错误日志
---【3个占位】
3、系统开小差 or innerCode高优错误
4、打点方法自身错误
5、美杜莎多语言异常监控
6、XSRF-TOKEN 未取到
7、XSRF-TOKEN 未取到（Chrome SameSite）

### 4.m

> pid: maizuo-msite
> name: 麦座前端m站
> [应用地址](https://clue.alibaba-inc.com/dashboard?pid=boss-react)
> [监控项地址](https://clue.alibaba-inc.com/project?pid=maizuo-msite&tab=custom)

#### 4-1.监控内容
1、接口报错（status = 200, 业务报错）
2、登录滑块验证错误
3、支付失败日志
4、微信sdk支付失败
5、MaiPay脚本加载失败
6、系统开小差 or innerCode高优错误
7、打点方法自身错误
8、美杜莎多语言异常监控
9、XSRF-TOKEN 未取到
10、XSRF-TOKEN 未取到（Chrome SameSite）

----

## toDo list

### boss-vue
处理资源误报
- [ ] 【异常事件】 需要修复一些第三方脚本报错，js报错
- [ ] 【资源监控错误】 https://maizuo.maitix.com/ 获取失败
- [ ] 【资源监控错误】 资源误报 def发布引起的 老版本 market.taobao.cm


## 附录
[Tracker 使用文档](https://yuque.antfin-inc.com/clue/help/wdylmk)