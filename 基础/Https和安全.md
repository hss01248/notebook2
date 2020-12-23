

# Https



# https握手流程

## 整个https连接到断开连接的流程图

![img](https://user-gold-cdn.xitu.io/2018/5/6/1633532f95052afd?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## https单向认证流程图

https://cloud.tencent.com/developer/article/1171381

![1jvsnqvhyp](https://gitee.com/hss012489/picbed/raw/master/picgo/1608689195586-1jvsnqvhyp.jpeg)



简化版:

![image-20201223125746990](https://gitee.com/hss012489/picbed/raw/master/picgo/1608699467065-image-20201223125746990.jpg)



## 双向认证流程图

https://cloud.tencent.com/developer/article/1171381

https://blog.csdn.net/u011511057/article/details/103825285

![在这里插入图片描述](https://gitee.com/hss012489/picbed/raw/master/picgo/1608542619694-watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE1MTEwNTc=,size_16,color_FFFFFF,t_70.jpg)

# 流程对应的okhttp/jdk代码

## 1 客户端ssl版本,支持的加密算法的指定

> 主要是配置

```java
ArrayList<ConnectionSpec> specs = new ArrayList<>();
specs.add(ConnectionSpec.CLEARTEXT);
specs.add(ConnectionSpec.COMPATIBLE_TLS);
specs.add(ConnectionSpec.RESTRICTED_TLS);
specs.add(ConnectionSpec.MODERN_TLS);

okhttpBuilder.connectionSpecs(specs)

相应的版本:
public static final ConnectionSpec MODERN_TLS = new Builder(true)
      .cipherSuites(APPROVED_CIPHER_SUITES)
      .tlsVersions(TlsVersion.TLS_1_3, TlsVersion.TLS_1_2, TlsVersion.TLS_1_1, TlsVersion.TLS_1_0)
      .supportsTlsExtensions(true)
      .build();
```

```java
也可以在sslsocketfactory里构建socket时指定:
((SSLSocket) socket).setEnabledProtocols(PROTOCOL_ARRAY);

 private static final String[] PROTOCOL_ARRAY;

    static {
        if (Build.VERSION.SDK_INT > Build.VERSION_CODES.N_MR1) {
            PROTOCOL_ARRAY = new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"};
        } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
            PROTOCOL_ARRAY = new String[]{"SSLv3", "TLSv1", "TLSv1.1", "TLSv1.2"};
        } else {
            PROTOCOL_ARRAY = new String[]{"SSLv3", "TLSv1"};
        }
    }

然后设置给okhttp:
builder.sslSocketFactory(new TLSCompactSocketFactory())
```

## 2 证书交互和校验

> 后续的操作主要围绕SSLContext来操作

```
   * @param km the sources of authentication keys or null 
     * @param tm the sources of peer authentication trust decisions or null
     * @param random the source of randomness for this generator or null
     * @throws KeyManagementException if this operation fails
     */
    public final void init(KeyManager[] km, TrustManager[] tm,
                                SecureRandom random)
                                
```

* KeyManager: 双向校验时,发送客户端的证书和公钥
* TrustManager: 校验服务端的证书的逻辑
* SecureRandom: 最后的随机密钥生成器

>  裸写socket代码时:

```
以下为样例，我们这里不打算从JDK的keytool建立的库中取证书，只是直接从一个已知的字符串中拿证书，所以直接实现一个简单的TrustManager和KeyManager：

sslcontext = SSLContext.getInstance("SSL");
sslcontext.init(new KeyManager[] {new SimpleKeyManager(privateKey, x509selfCertChain)}
                      ,
                      new TrustManager[] {new SimpleTrustManager(x509TrustedCert)}
                      ,
                      new SecureRandom ());
 

 

SimpleKeyManager实现了X509KeyManager接口，保存自己的证书内容和私钥。

SimpleTrustManager实现了TrustManager接口，保存了客户端信任的证书。

 

init后SSLContext初始化完成，此时可以调用SSLContext.getSocketFactory()取得SSLSocketFactory实例。然后用取到的factory来建立SSLSocket连接，当然，还需要提供Socket对象，host地址，host端口：

 

(SSLSocket) sslssFactory.createSocket(Socket s, String host, int port, boolean autoClose);
 

 

得到了SSLSocket之后的操作就跟普通socket一样了,socket.getOutputStream()

 

SSLSocket有几个选项设置SSL连接建立时的设置，如配置服务器模式或客户端模式，以下是较有用的几个：

 SSLSocket.setEnabledCipherSuites( new String[]{"SSL_RSA_WITH_RC4_128_MD5" });

 SSLSocket支持的密码套件。

 SSLSocket.setEnableSessionCreation( true );

 新的SSL可以此socket建立。

 SSLSocket.setNeedClientAuth( true );

 是否要求客户端身份验证，既校验证书。

 SSLSocket.setUseClientMode( false );

 握手时使用什么模式（客户端/服务器）。
```



Android常用的okhttp配置时

```java
private SSLContext getDefaultSslContext() {
        SSLContext sslContext = null;
        try {
            sslContext = SSLContext.getInstance("TLS");
            sslContext.init(null, new TrustManager[]{new X509TrustManager() {

                @Override
                public void checkClientTrusted(X509Certificate[] chain, String authType) {
                    //Android作为服务端时,校验客户端证书
                }

                @Override
                public void checkServerTrusted(X509Certificate[] chain, String authType) {
                    //Android作为客户端时,校验服务端证书
                    //只校验有效性,未校验与域名是否匹配
                    try {
                        for (int i = 0; i < chain.length; i++) {
                            chain[i].checkValidity();
                        }
                    } catch (CertificateExpiredException expiredException) {
                        expiredException.printStackTrace();
                        throw new RuntimeException(expiredException);
                    } catch (CertificateNotYetValidException e) {
                        e.printStackTrace();
                        throw new RuntimeException(e);
                    }
                }

                @Override
                public X509Certificate[] getAcceptedIssuers() {
                    return new X509Certificate[0];
                }
            }}, new SecureRandom());
        } catch (GeneralSecurityException e) {
            e.printStackTrace();
        }
        return sslContext;
    }
```



## 2.1 校验服务端证书

![image-20201105104506714](https://gitee.com/hss012489/picbed/raw/master/picgo/1608695992021-1604544306756-image-20201105104506714.jpg)

### 步骤1: 校验证书本身有效性-TrustManager.checkServerTrusted实现

```java
 @Override
                public void checkServerTrusted(X509Certificate[] chain, String authType) {
                    //Android作为客户端时,校验服务端证书
                    //只校验有效性,未校验与域名是否匹配
                    try {
                        for (int i = 0; i < chain.length; i++) {
                            chain[i].checkValidity();
                        }
                    } catch (CertificateExpiredException expiredException) {
                        expiredException.printStackTrace();
                        throw new RuntimeException(expiredException);
                    } catch (CertificateNotYetValidException e) {
                        e.printStackTrace();
                        throw new RuntimeException(e);
                    }
                }
```

可以在证书有效期快到期时告警

![image-20201105102520590](https://gitee.com/hss012489/picbed/raw/master/picgo/1608696096908-1604543120633-image-20201105102520590.jpg)







### 步骤2: 证书/公钥锁定的校验: 

>  校验证书和域名是否匹配

[wiki/HTTP公钥固定](https://zh.wikipedia.org/wiki/HTTP公钥固定)

2016年，[Netcraft](https://zh.wikipedia.org/w/index.php?title=Netcraft&action=edit&redlink=1)在有关SSL的调研中称，只有0.09%的证书在使用HTTP公钥固定

#### 方法1 okhttp的直接配置: 只针对公钥

```java
 okhttpBuilder.certificatePinner(new CertificatePinner.Builder()
                .add("*.github.com","公钥的sha256->base64字符串","证书链上一级的公钥的sha256->base64字符串")
                                  .add(hostname, "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=")
  //sha256/f5fNYvDJUKFsO51UowKkyKAlWXZXpaGK6Bah4yX9zmI=   使用全A的能使框架抛出异常,打印出正确的公钥base64值)
                .build());
```

#### 方法2: hostnameVerifier: 更大的自由度

```java
okhttpBuilder.hostnameVerifier(new HostnameVerifier() {
            @Override
            public boolean verify(String hostname, SSLSession session) {
                try {
                    Certificate[] certificates =  session.getPeerCertificates();
                    for (int i = 0; i < certificates.length; i++) {
                        //证书本身的指纹
                        ByteString.of(certificates[i].getEncoded()).sha256().hex();
                        //公钥的指纹,certificatePinner校验的就是这里
ByteString.of(certificates[i].getPublicKey().getEncoded()).sha256().hex();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return true;
            }
        });
```



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

### 怎么看公钥指纹/证书指纹:

![image-20201105101544316](https://gitee.com/hss012489/picbed/raw/master/picgo/1608696220981-1604542544353-image-20201105101544316.jpg)

### 测试知乎的域名:

无代理时:

![image-20201105095457246](https://gitee.com/hss012489/picbed/raw/master/picgo/1604541310060-image-20201105095457246.jpg)

![image-20201105095646121](https://gitee.com/hss012489/picbed/raw/master/picgo/1604541406164-image-20201105095646121.jpg)

chales代理:

![image-20201105100622585](https://gitee.com/hss012489/picbed/raw/master/picgo/1604541982623-image-20201105100622585.jpg)

![image-20201105100758478](https://gitee.com/hss012489/picbed/raw/master/picgo/1604542078508-image-20201105100758478.jpg)

> 可以通过证书的其他api来校验相关信息: 比如判定证书是谁签发的

![image-20201105102520590](https://gitee.com/hss012489/picbed/raw/master/picgo/1604543120633-image-20201105102520590.jpg)

![image-20201105102656334](https://gitee.com/hss012489/picbed/raw/master/picgo/1604543216368-image-20201105102656334.jpg)



## 2.2 双向校验时,客户端发送证书

### 服务端

>  双向校验需要服务端主动开启, 否则客户端不发起

```java
SSLSocket.setNeedClientAuth( true );

 //是否要求客户端身份验证，既校验证书。

 SSLSocket.setUseClientMode( false );

 //握手时使用什么模式（客户端/服务器）。
 
```

当然,对于nginx,生成客户端密钥对后,一个配置即可.

```properties
server {
        listen       443 ssl;
        server_name  www.yourdomain.com;
        ssl                  on;  
        ssl_certificate      /data/sslKey/server.crt;  #server公钥证书
        ssl_certificate_key  /data/sslKey/server.key;  #server私钥
        ssl_client_certificate /data/sslKey/client.crt;  #客户端公钥证书
        ssl_verify_client on;  #开启客户端证书验证  
        ssl_verify_depth 1

        location / {
            root   html;
            index  index.html index.htm;
        }
    }
```

### 客户端: 

>  使用keytool生成的密钥对的私钥来创建KeyManager即可

```java
private static KeyManager[] prepareKeyManager(InputStream bksFile, String password) {
        try {
            if (bksFile == null || password == null) return null;
            KeyStore clientKeyStore = KeyStore.getInstance("BKS");
            clientKeyStore.load(bksFile, password.toCharArray());
            KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
            keyManagerFactory.init(clientKeyStore, password.toCharArray());
            return keyManagerFactory.getKeyManagers();
        } catch (KeyStoreException e) {
           e(e);
        } catch (NoSuchAlgorithmException e) {
           e(e);
        } catch (UnrecoverableKeyException e) {
           e(e);
        } catch (CertificateException e) {
           e(e);
        } catch (IOException e) {
           e(e);
        } catch (Exception e) {
           e(e);
        }
        return null;
    }
```



- 

# 应用

## 忽略证书校验,通过所有证书

HostnameVerifier空实现,且

TrustManager里的checkServerTrusted()方法也空实现

## 使用客户端内置的一个服务端根证书来校验握手时服务端过来的证书的有效性

> 使用assets里面的根证书文件来签发一个证书

```
 private static javax.net.ssl.TrustManager[] prepareTrustManager(InputStream... certificates) {
        if (certificates == null || certificates.length <= 0) return null;
        try {
            CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
            KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
            keyStore.load(null);
            int index = 0;
            for (InputStream certificate : certificates) {
                String certificateAlias = Integer.toString(index++);
                keyStore.setCertificateEntry(certificateAlias, certificateFactory.generateCertificate(certificate));
                //签发一个证书
                try {
                    if (certificate != null) certificate.close();
                } catch (IOException e) {
                   e(e);
                }
            }
            TrustManagerFactory trustManagerFactory;
            trustManagerFactory = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
            trustManagerFactory.init(keyStore);
            return trustManagerFactory.getTrustManagers();
        } catch (NoSuchAlgorithmException e) {
           e(e);
        } catch (CertificateException e) {
           e(e);
        } catch (KeyStoreException e) {
           e(e);
        } catch (Exception e) {
           e(e);
        }
        return null;
    }
```





## 锁定公钥/锁定证书指纹

okhttpBuilder.certificatePinner()

或者

okhttpBuilder.hostnameVerifier

# 兼容问题处理

## Android4开启TLS1.2

> TLs1和1.1有不小的安全问题,所以有的服务端只支持TLS1.2. 那么Android20之下的手机访问这样的服务端时就会报错.

https://blog.csdn.net/yanzhenjie1003/article/details/80202476

https://blog.csdn.net/joye123/article/details/53888252

https://blog.csdn.net/guoxiaolongonly/article/details/82589069

![image-20201221174134653](https://gitee.com/hss012489/picbed/raw/master/picgo/1608543694706-image-20201221174134653.jpg)



## Android7.0以上的代理抓包配置问题

Android设备上根证书分两种;system,user

7.0以下,默认两种都接受

7.0开始,默认只接受系统内置证书.

未root时,chales,fiddler等抓包工具生成的证书只能安装到user目录下作为user证书.所以7.0以上抓不了包.

3种解决方式:

* 对okhttp设置忽略证书  只能作用于经过代码设置的okhttpclient. 
* 全局设置: 使用aop切入全局okhttpclient,但无法切入底层是urlconnection实现的第三方库
* 全局设置: manifest里,app标签下,设置下面的网络配置. **推荐使用此种方式**. debug时可抓包,release时只接受系统证书

```xml
android:networkSecurityConfig="@xml/network_security_config_httputil"
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <debug-overrides>
        <trust-anchors>
            <certificates src="system"/>
            <certificates src="user"/>
        </trust-anchors>
    </debug-overrides>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```



# 后续发展:Http3

参考  https://juejin.cn/post/6908522467107536903

HTTP/2因为底层使用的传输层协议仍然是TCP，所以他存在着TCP队头阻塞、TCP握手延时长以及协议僵化等问题。

这导致HTTP/2虽然使用了多路复用、二进制分帧等技术，但是仍然存在着优化空间

Http3/QUIC协议有以下特点：

- 基于UDP的传输层协议：它使用UDP端口号来识别指定机器上的特定服务器。
- 可靠性：虽然UDP是不可靠传输协议，但是QUIC在UDP的基础上做了些改造，使得**他提供了和TCP类似的可靠性**。它提供了数据包重传、拥塞控制、调整传输节奏以及其他一些TCP中存在的特性。
- 实现了无序、并发字节流：**QUIC的单个数据流可以保证有序交付，但多个数据流之间可能乱序**，这意味着单个数据流的传输是按序的，但是多个数据流中接收方收到的顺序可能与发送方的发送顺序不同！
- 快速握手：**QUIC提供0-RTT和1-RTT的连接建立**
- 使用TLS 1.3传输层安全协议：与更早的TLS版本相比，TLS 1.3有着很多优点，但使用它的最主要原因是其握手所花费的往返次数更低，从而能降低协议的延迟。

![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1608703621668-109e1e81438643d3aa50d8dfed33b7c6~tplv-k3u1fbpfcp-zoom-1.image)

![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1608703536948-0470f5d504444e4093c109939aa77b03~tplv-k3u1fbpfcp-zoom-1.image)

# 参考

https://my.oschina.net/bugly/blog/906636



![img](https://gitee.com/hss012489/picbed/raw/master/picgo/1608699597956-640.jpg)