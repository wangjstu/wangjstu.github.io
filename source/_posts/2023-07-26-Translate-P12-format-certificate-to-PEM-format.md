---
title: P12格式证书转PEM格式
date: 2023-07-26 18:52:26
tags:
- PEM
- P12
categories:
- Shell
toc: true
---

## 证书格式信息

### PEM格式
- PEM格式是证书颁发机构颁发证书的最常见格式.PEM证书通常具有扩展名，例如.pem，.crt，.cer和.key。它们是Base64编码的ASCII文件，包含“----- BEGIN CERTIFICATE -----”和“----- END CERTIFICATE -----”语句。服务器证书，中间证书和私钥都可以放入PEM格式。
- Apache和其他类似服务器使用PEM格式证书。几个PEM证书，甚至私钥，可以包含在一个文件中，一个在另一个文件之下，但是大多数平台（例如Apache）希望证书和私钥位于单独的文件中。

### DER格式
- DER格式只是证书的二进制形式，而不是ASCII PEM格式。它有时会有.der的文件扩展名，但它的文件扩展名通常是.cer所以判断DER .cer文件和PEM .cer文件之间区别的唯一方法是在文本编辑器中打开它并查找BEGIN / END语句。所有类型的证书和私钥都可以用DER格式编码。DER通常与Java平台一起使用。
- SSL转换器只能将证书转换为DER格式。

### PKCS＃7 / P7B格式
- PKCS＃7或P7B格式通常以Base64 ASCII格式存储，文件扩展名为.p7b或.p7c。P7B证书包含“----- BEGIN PKCS7 -----”和“----- END PKCS7 -----”语句。
- P7B文件仅包含证书和链证书，而不包含私钥。多个平台支持P7B文件，包括Microsoft Windows和Java Tomcat。
- p7r是CA对证书请求的回复，只用于导入。p7b以树状展示证书链(certificate chain)，同时也支持单个证书，不含私钥。

### PKCS＃12 / PFX格式
- PKCS＃12或PFX格式是二进制格式，用于将服务器证书，任何中间证书和私钥存储在一个可加密文件中。PFX文件通常具有扩展名，例如.pfx和.p12。PFX文件通常在Windows计算机上用于导入和导出证书和私钥。它规定了可包含所有私钥、公钥和证书。通常包含保护密码，PKCS#12的密钥库保护密码同时也用于保护Key。
- 将PFX文件转换为PEM格式时，OpenSSL会将所有证书和私钥放入一个文件中。您需要在文本编辑器中打开该文件，并将每个证书和私钥（包括BEGIN / END语句）复制到其各自的文本文件中，并将它们分别保存为certificate.cer，CACert.cer和privateKey.key。

### X.509
- X.509定义了两种证书：公钥证书和属性证书。PKCS#7和PKCS#12使用的都是公钥证书。PKCS＃7的SignedData的一种退化形式可以分发公钥证书和CRL。 一个SignedData可以包含多张公钥证书。PKCS＃12可以包含公钥证书及其私钥，也可包含整个证书链。

### Java KeyStore的类型
- JKS和JCEKS是Java密钥库(KeyStore)的两种比较常见类型(我所知道的共有5种，JKS, JCEKS, PKCS12, BKS，UBER)。JKS的Provider是SUN，在每个版本的JDK中都有，JCEKS的Provider是SUNJCE，1.4后我们都能够直接使用它。JCEKS在安全级别上要比JKS强，使用的Provider是JCEKS(推荐)，尤其在保护KeyStore中的私钥上（使用TripleDes）。

## 证书格式转换

### CONVERT PEM

- PEM TO DER
```
openssl x509 -outform der -in certificate.pem -out certificate.der
```

- PEM TO P7B
```
openssl crl2pkcs7 -nocrl -certfile certificate.cer -out certificate.p7b -certfile CACert.cer
```

- PEM TO PFX
```
openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt
```

### CONVERT DER

- DER(.CRT .CER .DER) TO PEM
```
openssl x509 -inform der -in certificate.cer -out certificate.pem
```

- DER TO CER
```
openssl x509 -inform der -in certificat-ssl.der -out certificat-ssl.cer
```

### CONVERT P7B

- P7B TO PEM
```
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
```

- P7B TO PFX
```
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer

openssl pkcs12 -export -in certificate.cer -inkey privateKey.key -out certificate.pfx -certfile CACert.cer
```

- P7B TO CER
```
openssl pkcs7 -print_certs -in certificat-ssl.p7b -out certificat-ssl.cer
```

### CONVERT PFX

- PFX TO PEM
```
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
```

### CONVERT CER

- CER TO P7B
```
openssl crl2pkcs7 -nocrl -certfile certificat-ssl.cer -certfile cert-intermediaire.cer -certfile cert-racine.cer -out certificat-ssl.p7b
```

- CER TO PFX
```
openssl pkcs12 -in certificat-ssl.cer -certfile cert-intermediaire.cer -certfile cert-racine.cer -inkey cle-privee.key -export -out certificat-ssl.pfx
```

- CER TO DER
```
openssl x509 -in certificat-ssl.cer -outform der -out certificat-ssl.der
```

### CONVERT P12

- 生成证书newfile.crt.pem(CURL设置使用CURLOPT_SSLCERT)
```
openssl pkcs12 -in path.p12 -out newfile.crt.pem -clcerts -nokeys
```

- 生成私钥newfile.key.pem(CURL设置使用CURLOPT_SSLKEY)
```
openssl pkcs12 -in path.p12 -out newfile.key.pem -nocerts -nodes
```

- 如果要将证书和密钥放在同一个文件中，且没有密码，请使用以下方法，因为空密码将导致密钥无法导出:
```
openssl pkcs12 -in path.p12 -out newfile.pem -nodes
```

- 或者，如果你想为私钥提供一个密码(CURL设置使用CURLOPT_SSLCERTPASSWD)，忽略-nodes并输入一个密码:
```
openssl pkcs12 -in path.p12 -out newfile.pem
```

- 如果您需要直接从命令行(例如脚本)输入PKCS#12密码，只需添加`-passin pass:${password}:`
```
openssl pkcs12 -in path.p12 -out newfile.crt.pem -clcerts -nokeys -passin 'pass:P@s5w0rD'
```

## 参考
- [Openssl源代码整理学习---含P7/P10/P12说明](https://www.cnblogs.com/testlife007/p/6888340.html)
- [ssl-converter](https://www.httpcs.com/en/ssl-converter)
- [ssl-doc](https://www.openssl.org/docs/man1.0.2/man1/pkcs12.html)