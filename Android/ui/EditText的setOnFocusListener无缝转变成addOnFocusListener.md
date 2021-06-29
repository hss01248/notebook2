# 如何将EditText的setOnFocusListener无缝转变成addOnFocusListener的效果

## 方式一: jarfilter插件法

将AppCompatEditText源码拷贝出来,同样包名建立一个同名类

用增加以下方法

用jarfilter插件排除掉appcompact包里的AppCompatEditText即可

注意: 如果appcompact包升级了,要check一下AppCompatEditText源码是否有新的,如果有,再拷贝一次.

```java
Set<OnFocusChangeListener> listeners = new LinkedHashSet<>();

/**
 * 实际效果=addOnXxxxListener
 * @param listener
 */
@Override
public void setOnFocusChangeListener(OnFocusChangeListener listener) {
    listeners.add(listener);
    super.setOnFocusChangeListener(new OnFocusChangeListener() {
        @Override
        public void onFocusChange(View v, boolean hasFocus) {
            if(listeners.size() >0){
                Iterator<OnFocusChangeListener> iterator = listeners.iterator();
                while (iterator.hasNext()){
                    OnFocusChangeListener next = iterator.next();
                    next.onFocusChange(v,hasFocus);
                }
            }
        }
    });
}
```

## 方法2: 使用asm或者javaassist

给AppCompatEditText增加属性和方法. 

属性和方法内容同上

参考

https://www.jianshu.com/p/609397fcac1a



# 方法三: 链式承接

调用onFocusChange之前,把别人设置的listener先调用一次,在调用自己的:

> 不如方法1

```java
@Override
    public void setOnFocusChangeListener(OnFocusChangeListener listener) {
        OnFocusChangeListener preListener =  getOnFocusChangeListener();
        super.setOnFocusChangeListener(new OnFocusChangeListener() {
            @Override
            public void onFocusChange(View v, boolean hasFocus) {
                if(preListener != null){
                    preListener.onFocusChange(v, hasFocus);
                }
                listener.onFocusChange(v,hasFocus);
            }
        });
    }
```



