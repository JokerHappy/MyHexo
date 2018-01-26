title: iOS RSA 加密
date: 2016-03-15 
categories: iOS
tags: [RSA]

---

##### RSA 简述

* RSA算法：1977年由`Ron Rivest`、`Adi Shamirh`和`LenAdleman`发明的，RSA就是取自他们三个人的名字。算法基于一个数论：将两个大素数相乘非常容易，但要对这个乘积的结果进行因式分解却非常困难，因此可以把乘积公开作为公钥。该算法能够抵抗目前已知的所有密码攻击。RSA算法是一种非对称算法，算法需要一对密钥，使用其中一个加密，需要使用另外一个才能解密。我们在进行RSA加密通讯时，就把公钥放在客户端，私钥留在服务器。

<!-- more --> 

[RSA算法原理（一）](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)<br/>
[RSA算法原理（二）](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)


##### 公钥私钥

[公钥与私钥](http://www.blogjava.net/yxhxj2006/archive/2012/10/15/389547.html)

* `DER`, `PEM`：既然使用RSA需要一对密钥，那么我们当然是要先使用工具来生成这样一对密钥了。在`linux`、`unix`下，最简单方便的就是使用`openssl`命令行了。而`DER`、`PEM`就是生成的密钥可选择的两种文件格式。`DER`是`Distinguished Encoding Rules`的简称，是一种信息传输语法规则，在`ITU X.690`中定义的。在`iOS`端，我们的公钥就是需要这样一种格式的，我们可以从[Certificate, Key, and Trust Services Reference](https://developer.apple.com/library/ios/documentation/Security/Reference/certifkeytrustservices/index.html#//apple_ref/c/func/SecCertificateCreateWithData)这篇文档的`SecCertificateCreateWithData`函数的`data`参数的说明中看到。而`PEM`格式是一种对`DER`进行封装的格式，他只是把`der`的内容进行了`base64`编码并加上了头尾说明。`openssl`命令行默认输出的都是`PEM`格式的文件，要能够在`iOS`下使用，我们需要指定使用`DER`或者先生成`PEM`然后转换称`DER`。

具体步骤：

生成私钥文件     
    
```
	$ openssl genrsa -out private.pem 1024

		openssl:是一个自由的软件组织,专注做加密和解密的框架。
		genrsa:指定了生成了算法使用RSA
		-out:后面的参数表示生成的key的输入文件
		1024:表示的是生成key的长度,单位字节(bits)
```

创建证书请求

```
    $ openssl req -new -key private.pem -out rsacert.csr

        可以拿着这个文件去数字证书颁发机构（即CA）申请一个数字证书。CA会给你一个新的文件cacert.pem,那才是你的数字证书。(要收费的)
```

生成证书并签名，有效期10年

```
    $ openssl x509 -req -days 3650 -in rsacert.csr -signkey private.pem -out rsacert.crt

        509是一种非常通用的证书格式。
        将用上面生成的密钥privkey.pem和rsacert.csr证书请求文件生成一个数字证书rsacert.crt。这个就是公钥
```

 转换格式 将 PEM 格式文件 转换成 DER 格式

```
    $ openssl x509 -outform der -in rsacert.crt -out rsacert.der

        在 iOS开发中,公钥是不能使用base64编码的,上面的命令是将公钥的base64编码字符串转换成二进制数据
```

导出 P12 文件

```
	$ openssl pkcs12 -export -out p.p12 -inkey private.pem -in rsacert.crt

        在iOS使用私钥不能直接使用，需要导出一个p12文件。下面命令就是将私钥文件导出为p12文件。

```

##### RSA使用

- 客户端向服务器第一次发起登录请求（不传输用户名和密码）。
- 服务器利用RSA算法产生一对公钥和私钥。并保留私钥， 将公钥发送给客户端。
- 客户端收到公钥后， 加密用户密码， 向服务器发起第二次登录请求（传输用户名和加密后的密码）。
- 服务器利用保留的私钥对密文进行解密，得到真正的密码。

拓展

-  客户端向服务器第一次发起登录请求（不传输用户名和密码）。
-     服务器利用RSA算法产生一对公钥和私钥。并保留私钥， 将公钥发送给客户端。
-     客户端收到公钥后， 加密用户密码，向服务器发送用户名和加密后的用户密码； 同时另外产生一对公钥和私钥，自己保留私钥, 向服务器发送公钥； 于是第二次登录请求传输了用户名和加密后的密码以及客户端生成的公钥。
-     服务器利用保留的私钥对密文进行解密，得到真正的密码。 经过判断， 确定用户可以登录后，生成sessionId和token， 同时利用客户端发送的公钥，对token进行加密。最后将sessionId和加密后的token返还给客户端。
-     客户端利用自己生成的私钥对token密文解密， 得到真正的token。

