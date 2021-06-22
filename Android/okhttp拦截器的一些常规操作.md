# Okhttp拦截器的一些常规操作

# 日志和各种ui查看信息:



- [Chucker](https://github.com/ChuckerTeam/chucker): An in-app HTTP inspector for Android OkHttp clients.
- [Flipper](https://fbflipper.com/): A desktop debugging platform for mobile developers.
- [OkLog](https://github.com/simonpercic/OkLog): Response logging interceptor for OkHttp. Logs a URL link with URL-encoded response for every OkHttp call.

# 添加公共请求头

> 不建议初始化网络库时放到一个map,然后每次读取
>
> 比较建议直接在拦截器里每次赋值

```java
 @Override
    public Response intercept(Chain chain) throws IOException {
        Request original = chain.request();
       
        Request.Builder builder = original.newBuilder();
        builder.addHeader(VERSION_CODE, code);
        builder.addHeader(DEVICE_TYPE, type);
        return chain.proceed(builder.build());
    }
```









# 对请求体进行gzip压缩

```java
Request compressedRequest = originalRequest.newBuilder()
        .header("Content-Encoding", "gzip")
        .method(originalRequest.method(), forceContentLength(gzip(originalRequest.body())))
        .build();
return chain.proceed(compressedRequest);


 private RequestBody gzip(final RequestBody body) {
        return new RequestBody() {
            @Override
            public MediaType contentType() {
                return body.contentType();
            }

            @Override
            public long contentLength() {
                return -1; // We don't know the compressed length in advance!
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
              //gzip压缩
                BufferedSink gzipSink = Okio.buffer(new GzipSink(sink));
                body.writeTo(gzipSink);
                gzipSink.close();
            }
        };
    }
//获取gzip后的大小
private RequestBody forceContentLength(final RequestBody requestBody) throws IOException {
        final Buffer buffer = new Buffer();
        requestBody.writeTo(buffer);

        final long size = buffer.size();

        return new RequestBody() {
            @Override
            public MediaType contentType() {
                return requestBody.contentType();
            }

            @Override
            public long contentLength() {
                return size;
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
                sink.write(buffer.snapshot());
               
            }
        };
    }
```





# 对请求体进行加密

> 下面的几行也是获取requestBody里字节流的通用方法

```java
 private RequestBody forceContentLength(final RequestBody requestBody) throws IOException {
      
   final Buffer buffer = new Buffer();
        requestBody.writeTo(buffer);

        final long size = buffer.size();


        final byte[] bytes = new byte[(int) size];
        buffer.readFully(bytes);

        final byte[] bytesEncrypted = encrypt(bytes);
        if(DeviceReporter.getConfig().isDebug()){
            Log.w("buffer","buffer.readFully(bytes)\n"+new String(bytes));
            Log.w("buffer","after encrypted\n"+new String(bytesEncrypted));

            Log.w("buffer","after decrypted\n"+new String(decrypt(bytesEncrypted)));
        }


        return new RequestBody() {
            @Override
            public MediaType contentType() {
                return requestBody.contentType();
            }

            @Override
            public long contentLength() {
                return size;
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
                sink.write(bytesEncrypted);
            }
        };
    }

private static final String AES = "AES";
private static final String CRYPT_KEY ="16位或32位长度字符串"
private Key getSecretKey() {
        return new SecretKeySpec(CRYPT_KEY.getBytes(), AES);
    }

    private byte[] encrypt(byte[] bytes) {
        try {
            InputStream is = new ByteArrayInputStream(bytes);//目标
            ByteArrayOutputStream out = new ByteArrayOutputStream(bytes.length);
            //SecretKey deskey = new SecretKeySpec(getKey().getBytes(), ENCRYPT_TYPE);
            //Key length not 128/192/256 bits
            Cipher cipher = Cipher.getInstance(AES);
            cipher.init(Cipher.ENCRYPT_MODE, getSecretKey());
            // 创建加密流
            CipherInputStream cis = new CipherInputStream(is, cipher);
            byte[] buffer = new byte[1024];
            int r;
            while ((r = cis.read(buffer)) > 0) {
                out.write(buffer, 0, r);
            }
            return out.toByteArray();
        }catch (Throwable throwable){
            XLogUtil.exception("aes",throwable);
            return bytes;
        }
    }

    private byte[] decrypt(byte[] bytes) {
        try {
            InputStream is = new ByteArrayInputStream(bytes);//目标
            ByteArrayOutputStream out = new ByteArrayOutputStream(bytes.length);
            //SecretKey deskey = new SecretKeySpec(getKey().getBytes(), ENCRYPT_TYPE);
            //Key length not 128/192/256 bits
            Cipher cipher = Cipher.getInstance(AES);
            cipher.init(Cipher.DECRYPT_MODE, getSecretKey());
            // 创建加密流
            CipherInputStream cis = new CipherInputStream(is, cipher);
            byte[] buffer = new byte[1024];
            int r;
            while ((r = cis.read(buffer)) > 0) {
                out.write(buffer, 0, r);
            }
            return out.toByteArray();
        }catch (Throwable throwable){
            throwable.printStackTrace();
            return bytes;
        }
    }
```



# 文件上传时获取文件路径:

```java
//拿到RequestBody源码里静态方法的参数file:

public static RequestBody create(final @Nullable MediaType contentType, final File file) {
    if (file == null) throw new NullPointerException("file == null");

    return new RequestBody() {
      @Override public @Nullable MediaType contentType() {
        return contentType;
      }

      @Override public long contentLength() {
        return file.length();
      }

      @Override public void writeTo(BufferedSink sink) throws IOException {
        Source source = null;
        try {
          source = Okio.source(file);
          sink.writeAll(source);
        } finally {
          Util.closeQuietly(source);
        }
      }
    };
  }
//通过下面的反射方法可以拿到:


private String getFilePath(RequestBody requestBody) {
        try {
            //ContentTypeOverridingRequestBody
            //RequestBody$3
            Class clazz = requestBody.getClass();
            String name = clazz.getName();
            if(name.contains("ContentTypeOverridingRequestBody")){
                Field field = clazz.getDeclaredField("delegate");
                field.setAccessible(true);
                requestBody = (RequestBody) field.get(requestBody);

                clazz = requestBody.getClass();
            }

            Field field = clazz.getDeclaredField("val$file");
            field.setAccessible(true);
            File file = (File) field.get(requestBody);
            return file.getAbsolutePath();


        }catch (Throwable throwable){
            throwable.printStackTrace();
        }
        return "";
    }
```



# 拦截重复请求(慎用)

一段时间内(比如30s)缓存response对象,判定请求是重复发起时,等待响应,如果后续响应成功,就返回"请求重复"的响应.否则继续发送.

关键在如何判断两个请求是否重复发送: url,时间间隔,请求参数,请求体,cookie

# 将cookie抛到应用拦截器层

默认情况下,普通应用拦截器无法拿到cookie信息,在下层被okhttp内置的拦截器透明处理了,如何在应用拦截器里也拿到cookie信息呢?

创建一个网络拦截器,在拦截器层保存请求和响应的cookie信息,用另外的header字段,回写到响应里:

```java
public class PrintCookieNetworkInterceptor implements Interceptor {



    public static final String KEY_REQUEST_COOKIE = "log-request-cookie";
    public static final String KEY_REQUEST_CONTENT_LENGTH = "log-request-Content-Length";
    public static final String KEY_REQUEST_CONTENT_TYPE = "log-request-Content-Type";
    public static final String KEY_REQUEST_ACCEPT_ENCODING = "log-request-Accet-Encoding";

    public static final String KEY_SET_COOKIE = "log-response-cookie";
    public static final String KEY_RESPONSE_CONTENT_LENGTH = "log-response-Content-Length";
    public static final String KEY_RESPONSE_CONTENT_ENCODING = "log-response-Content-Encoding";
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request request = chain.request();

        String requestCookie = request.header("Cookie");
        if(requestCookie == null){
            requestCookie = "";
        }
        String requestContentLength = request.header("Content-Length");
        if(requestContentLength == null){
            requestContentLength = "";
        }
        String requestContentType = request.header("Content-Type");
        if(requestContentType == null){
            requestContentType = "";
        }
        String requestAcceptEncoding = request.header("Accept-Encoding");
        if(requestAcceptEncoding == null){
            requestAcceptEncoding = "";
        }


       Response response =  chain.proceed(request);

        if(!ChuckInterceptor.isEnable()){
            return response;
        }


        List<String> cookieStrings = response.headers().values("Set-Cookie");
        String responseCookie = "";

        if(cookieStrings!= null && !cookieStrings.isEmpty()){
            responseCookie = Arrays.toString(cookieStrings.toArray());
        }
        String length = response.header("Content-Length");
        if(length == null){
            length = "";
        }
        String encoding = response.header("Content-Encoding");
        if(encoding == null){
            encoding = "";
        }


        response = response.newBuilder()
                .header(KEY_REQUEST_ACCEPT_ENCODING,requestAcceptEncoding)
                .header(KEY_REQUEST_CONTENT_LENGTH,requestContentLength)
                .header(KEY_REQUEST_CONTENT_TYPE,requestContentType)
                .header(KEY_REQUEST_COOKIE,requestCookie)
                .header(KEY_SET_COOKIE,responseCookie)
                .header(KEY_RESPONSE_CONTENT_LENGTH,length)
                .header(KEY_RESPONSE_CONTENT_ENCODING,encoding)
                .build();
        return response;
    }
}
```







