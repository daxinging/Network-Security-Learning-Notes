## 思维导图

![](image44/QQ截图20220405211705.png)







**思路说明**：
反编译提取 URL 或抓包获取 URL，进行 WEB 应用测试，如不存在或走其他协议的情况下，需采用网络接口抓包进行数据获取，转至其他协议安全测试！
**APP->WEB APP->其他 APP->逆向**
**WEB 抓包，其他协议抓包演示及说明**
**未逆向层面进行抓包区分各协议测试**
**逆向层面进行提取 APK 代码层面数据**
https://www.cnblogs.com/L0ading/p/12388928.html



## 案例演示：

### 抓包工具 WEB 协议面使用说明

- Burpsuite使用
- Charles软件使用
- 抓包精灵app使用
- fidder等

#### Burpsuite

安卓模拟器设置代理

鼠标左键长按 

修改网络

![](image44/QQ截图20220406212122.png)

设置主机IP和端口号

![](image44/QQ截图20220406212507.png)

![](image44/QQ截图20220406212400.png)

![](image44/QQ截图20220406212614.png)

### 

![](image44/QQ截图20220406212853.png)

模拟器操作app，产生大量数据包history，进行筛选

![](image44/QQ截图20220406213104.png)



查看记录

访问网址，这个app的网址电脑也能访问，这就是个web站点

![](image44/QQ截图20220406213549.png)

#### Charles软件使用

![](image44/QQ截图20220406221452.png)



![](image44/QQ截图20220406214307.png)



![](image44/QQ截图20220406221425.png)



![](image44/QQ截图20220406221224.png)



电脑浏览器数据包

![QQ截图20220406221956](image44/QQ截图20220406221956.png)

手机数据包

![](image44/QQ截图20220406221916.png)



#### 抓包精灵app使用

![](image44/QQ截图20220406222604.png)



![QQ截图20220406222624](image44/QQ截图20220406222624.png)

### 抓包工具非 WEB 协议面使用说明

#### wireshark

![](image44/QQ截图20220406235116.png)

### 安卓逆向便捷 APK 一键提取 URL 演示

![](image44/QQ截图20220406235821.png)

![](image44/QQ截图20220406235920.png)

![](image44/QQ截图20220407000119.png)

![](image44/QQ截图20220407000218.png)



### 利用 Burp 筛选及联动功能打出军体拳

xray 配置证书

![](image44/QQ截图20220407000953.png)



![](image44/QQ截图20220407001154.png)



![](image44/QQ截图20220407001633.png)

![QQ截图20220407001701](image44/QQ截图20220407001701.png)

![QQ截图20220407001717](image44/QQ截图20220407001717.png)



xray 下载地址 [Releases · chaitin/xray (github.com)](https://github.com/chaitin/xray/releases)

![](image44/QQ截图20220407001847.png)

webscan --listen 127.0.0.1 :6666



![](image44/QQ截图20220407002442.png)



### 模拟器四个违法案例 APP 安全分析测试



## 涉及资源：

https://www.wireshark.org/download.html
https://www.charlesproxy.com/latest-release/download.do
https://github.com/huolizhuminh/NetWorkPacketCapture/releases
https://pan.baidu.com/s/1UGro7nQoBpT9vYRONsQi4w 提取码：xiao