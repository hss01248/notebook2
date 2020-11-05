# Https和安全

# 公钥固定

[wiki/HTTP公钥固定](https://zh.wikipedia.org/wiki/HTTP公钥固定)

![image-20200716144420182](https://cdn.jsdelivr.net/gh/hss01248/picbed@master/uPic/image-20200716144420182.png)

> 参考: https://mp.weixin.qq.com/s/RxtTrxM4DzwxESiKDEoBEA

![img](https://mmbiz.qpic.cn/mmbiz_png/Baq5lYpIw7Umo2UJf6AEfCl7Eiax5Zf4B5THicCfc62WowiafYANUhjAj8Tn8ttaVxHD4ZZiaLibclNsNboUIicBH08A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 相关api

流程:

![image-20201105104506714](https://gitee.com/hss012489/picbed/raw/master/picgo/1604544306756-image-20201105104506714.jpg)



> checkServerTrusted  校验服务端证书本身是否有问题 比如是否过期,是否是代理

```java
new X509TrustManager() {
    @Override
    public void checkClientTrusted(X509Certificate[] chain, String authType) {
        Log.d(OkhttpAspect.TAG,"checkClientTrusted-证书校验开始,直接忽略校验:"+ chain);
    }

    @Override
    public void checkServerTrusted(X509Certificate[] chain, String authType) {
        for (int i = 0; i < chain.length; i++) {
            X509Certificate certificate = chain[i];
            //Tool.logw(ByteString.of(certificate.getPublicKey().getEncoded()).sha256().hex().toString());
            //Tool.logJson(certificate);
        }
        Log.d(OkhttpAspect.TAG,"checkServerTrusted-证书校验开始,直接忽略校验:"+ chain);
    }
```



> hostnameVerifier 校验该域名下的证书是否有问题,比如验证公钥指纹,证书指纹 

```java
builder.hostnameVerifier(DO_NOT_VERIFY)

HostnameVerifier DO_NOT_VERIFY = new HostnameVerifier() {
            //@Override
            public boolean verify(String hostname, SSLSession session) {
                Log.d(OkhttpAspect.TAG,"HostnameVerifier-域名校验开始,直接忽略校验:"+hostname);
               Certificate[] peerCertificates = session.getPeerCertificates();
                return true;
            }
        };
```



> 证书-Certificate类的相关操作:

```java
@Override
public boolean verify(String hostname, SSLSession session) {
    Log.d(OkhttpAspect.TAG, "HostnameVerifier-域名校验开始:" + hostname);

    try {
        Certificate[] peerCertificates = session.getPeerCertificates();
        for (int i = 0; i < peerCertificates.length; i++) {
            Certificate certificate = peerCertificates[i];
            Log.i(OkhttpAspect.TAG, "getPublicKey().getAlgorithm:" + certificate.getPublicKey().getAlgorithm());
            Log.i(OkhttpAspect.TAG, "getPublicKey().getFormat:" + certificate.getPublicKey().getFormat());

            Log.i(OkhttpAspect.TAG, "certificate.getPublicKey-sha256->toAsciiUppercase:" + ByteString.of(certificate.getPublicKey().getEncoded()).sha256().toAsciiUppercase());
            Log.i(OkhttpAspect.TAG, "certificate.getPublicKey-sha256->hex:" + ByteString.of(certificate.getPublicKey().getEncoded()).sha256().hex());
            Log.i(OkhttpAspect.TAG, "certificate.getPublicKey-sha256->base64:" + ByteString.of(certificate.getPublicKey().getEncoded()).sha256().base64());
            Log.i(OkhttpAspect.TAG, "certificate-sha256->hex:" + ByteString.of(certificate.getEncoded()).sha256().hex());
            //Log.i(OkhttpAspect.TAG, "sha256->utf-8:"+ByteString.of(certificate.getPublicKey().getEncoded()).sha256().utf8());
        }
        Log.i(OkhttpAspect.TAG, Arrays.toString(peerCertificates));
    } catch (Throwable throwable) {
        throwable.printStackTrace();
    }
```

![image-20201105102520590](https://gitee.com/hss012489/picbed/raw/master/picgo/1604543120633-image-20201105102520590.jpg)



## 测试知乎的域名:

无代理时:

![image-20201105095457246](https://gitee.com/hss012489/picbed/raw/master/picgo/1604541310060-image-20201105095457246.jpg)

![image-20201105095646121](https://gitee.com/hss012489/picbed/raw/master/picgo/1604541406164-image-20201105095646121.jpg)

chales代理:

![image-20201105100622585](https://gitee.com/hss012489/picbed/raw/master/picgo/1604541982623-image-20201105100622585.jpg)

![image-20201105100758478](https://gitee.com/hss012489/picbed/raw/master/picgo/1604542078508-image-20201105100758478.jpg)

> 可以通过证书的其他api来校验相关信息: 比如判定证书是谁签发的

![image-20201105102520590](https://gitee.com/hss012489/picbed/raw/master/picgo/1604543120633-image-20201105102520590.jpg)

![image-20201105102656334](https://gitee.com/hss012489/picbed/raw/master/picgo/1604543216368-image-20201105102656334.jpg)

## 证书的几个校验值:

> certificate.getPublicKey->sha256->hex:

公钥指纹

> certificate.getPublicKey->sha256->base64:

okhttp Builder的certificatePinner  公钥锁定

```java
CertificatePinner certificatePinner = new CertificatePinner.Builder()
        .add(hostname, "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
  //sha256/f5fNYvDJUKFsO51UowKkyKAlWXZXpaGK6Bah4yX9zmI=   使用全A的能使框架抛出异常,打印出正确的公钥base64值)
        .build();
OkHttpClient.Builder client = new OkHttpClient.Builder();
client.certificatePinner(certificatePinner);
```

> certificate->sha256->hex

证书的指纹信息,与在chrome上看到的证书界面最下方一致:

![image-20201105101544316](https://gitee.com/hss012489/picbed/raw/master/picgo/1604542544353-image-20201105101544316.jpg)

## 疑问: 是要锁定/校验公钥指纹还是锁定证书/校验证书指纹?

https://www.cnblogs.com/amyzhu/p/11838665.html

### 2.证书锁定原理

证书锁定（SSL/TLS Pinning）提供了两种锁定方式：

- Certificate Pinning，证书锁定
- Public Key Pinning，公钥锁定

#### 2.1 证书锁定

- 具体操作：将APP代码内置仅接受指定域名的证书，而不接受操作系统或者浏览器内置的CA根证书对应的任何证书。通过这种授权方式，保障了APP与服务端通信的唯一性和安全性，因此移动端APP与服务端（例如API网关）之间的通信可以保证绝对的安全。
- 缺点：CA签发证书存在有效期问题，在证书续期后需要将证书重新内置到APP内。

#### 2.2 公钥锁定

- 具体做法：公钥锁定是提前证书中的公钥并内置到移动端APP内，通过与服务器对比公钥值来验证连接的合法性。
- 优点：在制作证书密钥时，公钥在证书续期前后可以保持不变（即密钥对不变），所以可以避免证书有效期问题。

## 应用

> 忽略证书



> 锁定公钥/锁定证书指纹