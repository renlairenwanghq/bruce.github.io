# 数字签名、数字证书、openssl

## 1. RSA(非对称加密)

包含一个由公钥和私钥组成密钥对，公钥加密，必须通过私钥解密，私钥加密，必须通过公钥解密。

## 2. 数字签名

参考[数字证书与签名](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature)，先用Hash函数，生成信件的摘要（digest）,然后对摘要加密，就是数字签名。

## 3. 数字证书

参考[数字证书与签名](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature)，鲍勃去找"证书中心"（certificate authority，简称CA），为公钥做认证。证书中心用自己的私钥，对鲍勃的公钥和一些相关信息一起加密，生成"数字证书"（Digital Certificate）。

## 4. CRT、CER 

参考[openssl、x509、crt、cer、key、csr、ssl、tls 这些都是什么鬼?](http://www.cnblogs.com/lan1x/p/5872915.html)	

CSR 是Certificate Signing Request的缩写，即证书签名请求，这不是证书，可以简单理解成公钥，生成证书时要把这个提交给权威的证书颁发机构。

CRT 即 certificate的缩写，即证书。

## 5. SSL/TLS

TLS：传输层安全协议 Transport Layer Security的缩写

SSL：安全套接字层 Secure Socket Layer的缩写

TLS与SSL对于不是专业搞安全的开发人员来讲，可以认为是差不多的，这二者是并列关系，详细差异见 <http://kb.cnblogs.com/page/197396/>

## 6. 如何使用openssl生成RSA公钥和私钥对

参考链接https://blog.csdn.net/scape1989/article/details/18959657

## 7. 用openssl命令行取得服务器证书

参考链接https://blog.csdn.net/idisposable/article/details/46453613

## 8. openssl 给自己颁发证书的步骤

参考[openssl、x509、crt、cer、key、csr、ssl、tls 这些都是什么鬼?](http://www.cnblogs.com/lan1x/p/5872915.html)	

## 8. 编译

参考链接[openssl官方文档](https://wiki.openssl.org/index.php/Compilation_and_Installation)4.1项

## 9. 交叉编译

参考链接[openssl官方文档](https://wiki.openssl.org/index.php/Compilation_and_Installation)5.1.2项

## 参考链接

[openssl用法详解](https://www.cnblogs.com/yangxiaolan/p/6256838.html)

[openssl命令---s_client](https://blog.csdn.net/as3luyuan123/article/details/16812071)

[libcurl](https://curl.haxx.se/libcurl/c/)

[数字证书与签名](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature)