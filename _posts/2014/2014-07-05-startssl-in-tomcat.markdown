---
layout: post
title: "Tomcat中配置HTTPS证书"
subtitle: "以StartSSL为例"
date: 2014-07-05 10:24:56 +0800
comments: true
categories: Technology
tags: [startssl, tomcat, https, ItJustWorks]
---

### 背景
由于移动设备经常访问连接各种不可靠的无线网络，用户密码被嗅探的风险比较大，因此对与敏感信息需要加密传输。 而 HTTPS 是一种相对成熟的方案。

使用 HTTPS 协议用于移动应用的数据传输，随着App数量越来越多而显得更强烈。

startssl.com 提供一个免费的 ssl 证书，个人测试使用应该没问题。

### 从 startssl 获取私钥和证书
首先在 `startssl.com` 注册帐号，根据提示操作，这个过程比较漫长。注意填写个人信息时要详细（至少看起来是真实的地址）。 注册的攻略在网上能看到很多。 备份个人证书，否则以后换台电脑就不能登录做管理操作了。

帐号 Ready 后，根据提示创建域名的证书，这个步骤可以得到两个文件，分别是以 `.key` 结尾的私钥文件和以 `.crt` 为结尾的证书文件。保存好这两个文件，并记住私钥文件的密码备用。

还需要另外两个文件分别是 [ca.pem](http://www.startssl.com/certs/ca.pem) 和 [sub.class1.server.ca.pem](http://www.startssl.com/certs/sub.class1.server.ca.pem)。下载备用。

注：以下以域名 api.example.com 为例，实际使用请换成你自己的域名。

现在的4个文件分别为：

*    ssl.key
*    api.example.com.crt
*    ca.pem
*    sub.class1.server.ca.pem

### 生成 tomcat 使用的 keystore 文件

Tomcat 支持两种模式的配置方式，分别是 `BIO` 和 `NIO` 使用 `JSSE` 风格（使用 keystoreFile ）；`APR`/`native` 使用 `APR` 风格（使用 SSLCertificateFile / SSLCertificateKeyFile 指定私钥和证书）。

因为我们使用了 `NIO` ，所以按照 `JSSE` 风格配置，生成 keystore 文件。

首先将 key 文件和 crt 文件合并导出为 `p12` 。这个步骤需要输入私钥的密码，并指定一个新的导出密码。

    openssl pkcs12 -export -in ../api.example.com.crt -inkey ../ssl__.key \
        -out tomcat-startssl.p12 -name api.example.com -CApath ../


然后生成 keystore 文件，需要输入上一步的导出密码，及指定新的 keystore 密码，后面几步的导入需要用到这个密码。

    keytool -importkeystore -srckeystore tomcat-startssl.p12 -srcstoretype PKCS12 \
        -destkeystore startssl-api.example.com.jks


导入 startssl 的 CA

    keytool -keystore startssl-api.example.com.jks -import -trustcacerts \
        -alias startcom.ca -file ../ca.pem


导入 startssl 的 sub1

    keytool -keystore startssl-api.example.com.jks -import -trustcacerts \
        -alias startcom.ca.sub1 -file ../sub.class1.server.ca.pem

现在已经生成了一个可用的 `startssl-api.example.com.jks`

### 配置 tomcat

在 tomcat 的server.xml中找到相关的 Connector 部分，这部分默认已被注释掉，去掉注释并调整内容如下

    <Connector SSLEnabled="true" acceptCount="100" clientAuth="false"
        disableUploadTimeout="true" enableLookups="false" maxThreads="25"
        port="8443" keystoreFile="/etc/tomcat/startssl-api.example.com.jks" keystorePass="passw0rd"
        protocol="org.apache.coyote.http11.Http11NioProtocol" scheme="https"
        secure="true" sslProtocol="TLS" />

重启 tomcat，访问 8443 端口试试。点击浏览器地址栏网址左侧的验证标志，可以检验证书的内容。

### 总结

通过简单的配置，将服务有 http 迁移到更安全的 https 服务。


补充1： 本文的方法因为偷懒直接使用了 startssl 来为我们管理私钥文件，从安全的角度，大部分情况下我们应该自己保管这个文件。

补充2： 现在部署 HTTPS 更好的方式是用 Nginx 做 `SSL offloading` ，而实际的业务服务器仍然使用 HTTP 提供服务。

补充3： startssl 个人使用没问题，如果是企业使用，建议购买有商业支持的证书。

#### 参考文档

*    http://tomcat.apache.org/tomcat-7.0-doc/ssl-howto.html