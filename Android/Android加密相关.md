# Android加密相关

# 问题分解

* 加密算法

* 密钥管理- 密钥库

  https://developer.android.com/training/articles/keystore

* api兼容性

  在 Android 6.0 之前，密钥库仅支持 RSA 和 ECDSA

  在 Android 6.0 中，密钥库得到显著增强，增加了对 AES 和 HMAC 的支持

  https://developer.android.com/guide/topics/security/cryptography

  | 内容            | API要求                | 说明     |
  | --------------- | ---------------------- | -------- |
  | AndroidKeyStore | 18+                    | 密钥管理 |
  | AndroidCAStore  | 14+                    | 密钥管理 |
  | AES,RSA         | 基于javaapi,在1+就支持 | 加解密   |
  |                 |                        |          |
  |                 |                        |          |
  |                 |                        |          |
  |                 |                        |          |

  ### 能够从Android18+开始支持的:

  1.Cipher  

  | RSA/ECB/NoPadding    | 18+  |      |
  | -------------------- | ---- | ---- |
  | RSA/ECB/PKCS1Padding | 18+  |      |

  ### 2.KeyStore

  KeyStore 支持的密钥类型与 [`KeyPairGenerator`](https://developer.android.com/training/articles/keystore#SupportedKeyPairGenerators) 和 [`KeyGenerator`](https://developer.android.com/training/articles/keystore#SupportedKeyGenerators) 支持的相同。

  ### KeyPairGenerator

  | RSA  | 18+  | 支持的大小：512、768、1024、2048、3072、4096支持的公开指数：3、65537默认公开指数：65537 |
  | ---- | ---- | ------------------------------------------------------------ |
  |      |      |                                                              |

### KeyGenerator只支持api 23+

### 3.Signature

| SHA256withRSA | 18+  |
| ------------- | ---- |
|               |      |

* 便捷使用库

   [Security 库版本 1.1.0](https://developer.android.com/jetpack/androidx/releases/security#version_110_2) 需要Android api >=21  便捷操作file和sp

> 综上,加解密使用JAVA api,推荐使用AES对称加密.
>
> 密钥管理:
>
> 使用AndroidKeyStore的RSA加密后存储在本地,使用时再从本地读取,通过AndroidKeyStore里的私钥解密拿到真正的密钥,用于解密.此方案可兼容到18+. 
>
> 18以下的,使用特定的seed输入到SecureRandom,直接内存计算得到密钥.
>
> 如果需要兼容到Android6之前,就使用RSA.



# 另一种方式

将密钥和加密算法都放到c中实现,同时在每次对so的调用时校验context的签名信息,签名不对,直接构建c层crash.

# 名词

Android 密钥库系统



# 兼容性











# 参考

https://developer.android.com/guide/topics/security/cryptography

[Android密钥库的发展历史和使用指南](https://blog.csdn.net/weixin_33866037/article/details/86745902)

[Android 密钥库系统](https://blog.csdn.net/sky1203850702/article/details/53434914)

