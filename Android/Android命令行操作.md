# Android命令行操作

# 何谓命令行



# Android上怎么执行命令行

```java
Process execProcess = Runtime.getRuntime().exec(String command, String[] envp, File dir)
        throws IOException
```

获取命令行输出:

```java
BufferedReader reader =new BufferedReader(new InputStreamReader(execProcess
                    .getInputStream()), 8192)
  String line;
            while ((line = reader.readLine()) != null) {
                result = line;
            }
```



# 一些常见操作

##  读取自己进程打印的logcat输出:

> 参考: com.didichuxing.doraemonkit.kit.loginfo.helper.LogcatHelper

```java
private static List<String> getLogcatArgs(String buffer) {
        List<String> args = new ArrayList<>(Arrays.asList("logcat", "-v", "time"));

        // for some reason, adding -b main excludes log output from AndroidRuntime runtime exceptions,
        // whereas just leaving it blank keeps them in.  So do not specify the buffer if it is "main"
        if (!buffer.equals(BUFFER_MAIN)) {
            args.add("-b");
            args.add(buffer);
        }
   args.add("-d");// -d just dumps the whole thing

        return args;
    }
```

