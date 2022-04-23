

## 思维导图

![](image47/20210515225218.png)

#漏洞发现触发 WAF 点-针对 xray,awvs 等
1.扫描速度-(代理池，延迟，白名单等)
2.工具指纹-(特征修改，伪造模拟真实用户等)
3.漏洞 Payload-(数据变异，数据加密，白名单等)

## 案例演示：

### 代理池 Proxy_pool 项目搭建及使用解释

Proxy_pool —github地址

[https://github.com/jhao104/proxy_pool](https://github.com/jhao104/proxy_pool)

需要搭配redis数据库自行安装！

设置

![](image47/QQ截图20220408193159.png)

安装需要的库文件

```python
pip install -r requirements.txt
```

![](image47/QQ截图20220408193056.png)



![](image47/QQ截图20220408192749.png)

![](image47/QQ截图20220408200031.png)



![](image47/QQ截图20220408200407.png)

![QQ截图20220408200414](image47/QQ截图20220408200414.png)

![QQ截图20220408200449](image47/QQ截图20220408200449.png)

![QQ截图20220408200504](image47/QQ截图20220408200504.png)



总之，用着贼麻烦！

### 充钱代理池直接干 safedog+BT+Aliyun 探针

https://www.kuaidaili.com/ 

买隧道代理的，每次请求都更改ip，稳定

![](image47/QQ截图20220408201301.png)

设置ip 免密登录

![](image47/QQ截图20220408201436.png)



配置到脚本中

![](image47/QQ截图20220408201632.png)

运行

![](image47/QQ截图20220408201710.png)

网站依旧可以正常访问！效果稳的一批！

![](image47/QQ截图20220408201744.png)

宝塔日志，如果拦截，拦截的也是代理的IP，不管我们鸟事！

![](image47/QQ截图20220408201859.png)

### Safedog-awvs 漏扫注入测试绕过-延时,白名单



![QQ截图20220408202448](image47/QQ截图20220408202448.png)

扫描速度快，被拦截

![](image47/QQ截图20220408202734.png)

#### 延时

改为慢速

![](image47/QQ截图20220408202924.png)



网站正常访问，只不过扫描的很慢而已。

![](image47/QQ截图20220408203026.png)

#### 白名单

更改User-Agent 冒充爬虫白名单，快速扫描

![QQ截图20220408203153](image47/QQ截图20220408203153.png)

不会被拦截，正常访问

![](image47/QQ截图20220408203205.png)

控制速度，可以转发给burp suite 然后控制BP放包速度，（手动，或者按键精灵）

### Aliyun_os-awvs 漏扫注入测试绕过-延时，白名单

扫描速度快，站点直接GG，两个小时左右不能访问

### BT(baota)-awvs+xray 漏扫 Payload 绕过-延时，被动

![QQ截图20220408204503](image47/QQ截图20220408204503.png)

![QQ截图20220408204551](image47/QQ截图20220408204551.png)

转发给代理

![QQ截图20220408204624](image47/QQ截图20220408204624.png)



拦截端口

![](image47/QQ截图20220408205205.png)

出口转发

![QQ截图20220408204715](image47/QQ截图20220408204715.png)

监听端口

![QQ截图20220408204657](image47/QQ截图20220408204657.png)

三者联动（AWVS +Burpsuite +Xray）

![](image47/QQ截图20220408204805.png)



### 充钱代理池直接干 Safedog+BT+AliyunOS 漏洞发现

![](image47/QQ截图20220408205900.png)

![QQ截图20220408205535](image47/QQ截图20220408205535.png)

![QQ截图20220408205641](image47/QQ截图20220408205641.png)



![QQ截图20220408205558](image47/QQ截图20220408205558.png)

![QQ截图20220408205737](image47/QQ截图20220408205737.png)



![](image47/QQ截图20220408210115.png)



没有被拦截

![](image47/QQ截图20220408210401.png)

![QQ截图20220408205522](image47/QQ截图20220408205522.png)



手工测试

and 1=1 安全狗拦截

![QQ截图20220408210317](image47/QQ截图20220408210317.png)

xor 1 = 1 BT的防火墙拦截 

![QQ截图20220408210301](image47/QQ截图20220408210301.png)

  绕waf真不容易，哈哈哈，基本GG

每个工具的payload不一样，有点可能会触发waf被拦截，有点就不会！

工具要多试试用！



## 涉及资源：

[http://httpbin.org/ip](http://httpbin.org/ip)
[https://www.kuaidaili.com/](https://www.kuaidaili.com/)
[https://github.com/jhao104/proxy_pool](https://github.com/jhao104/proxy_pool)



```python
#配合代理池吃瓜扫描
#Author:小迪安全
#QQ471656814
import requests
import time
headers={
'Connection': 'keep-alive',
'Cache-Control': 'max-age=0',
'Upgrade-Insecure-Requests': '1',
'User-Agent': 'Mozilla/5.0 (compatible; Baiduspider-render/2.0;
+http://www.baidu.com/search/spider.html)',
'Sec-Fetch-Dest': 'document',
'Accept':
'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/si
gned-exchange;v=b3;q=0.9',
'Sec-Fetch-Site': 'none',
'Sec-Fetch-Mode': 'navigate',
'Sec-Fetch-User': '?1',
'Accept-Encoding': 'gzip, deflate, br',
'Accept-Language': 'zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7',
'Cookie': 'www.xiaodi8.com',
}
for paths in open('php_b.txt',encoding='utf-8'):
	url='http://www.xiaodi8.com/'
	paths=paths.replace('\n','')
	urls=url+paths
    proxy = {
       'http': 'tps116.kdlapi.com:15818',
        }
	try:
		code=requests.get(urls,headers=headers,proxies=proxy).status_code
		print(urls+'|'+str(code))
		if code==200 or code==403:
			print(urls+'|'+str(code))
		except Exception as err:
			print('connecting error')
			time.sleep(3)
```

