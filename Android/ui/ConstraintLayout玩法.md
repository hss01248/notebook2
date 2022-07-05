# ConstraintLayout玩法

# 1 同一行,左右排列,互怼,某一边必须一行,某一边自动换行

![image-20220705193905549](https://cdn.jsdelivr.net/gh/shuiniuhss/myimages@main/imagemac2/1657021152770-image-20220705193905549.jpg)

实现方式:

1 首尾互相约束成链(chain)

2 链头部item声明chain的空间排布方式: app:layout_constraintHorizontal_chainStyle="spread_inside"

3    自动换行的item : android:layout_width="0dp" +  app:layout_constraintHorizontal_weight="1"

```xml
<androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_marginTop="16dp"
        android:layout_height="wrap_content">  
		<TextView
        android:text="@string/xxxxxxxxxxxxxxx"
        android:textSize="14dp"
        android:textColor="#666666"
        android:fontFamily="@font/roboto_medium"
        android:id="@+id/tv_pay_await_status"
        app:layout_constraintTop_toBottomOf="@id/tv_pay_success_time"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginLeft="28dp"
        app:layout_constraintRight_toLeftOf="@id/tv_pay_await_amount"
        android:layout_marginTop="16dp"
        android:layout_width="0dp"
        app:layout_constraintHorizontal_chainStyle="spread_inside"
        app:layout_constraintHorizontal_weight="1"
        android:layout_marginBottom="2dp"
        android:layout_height="wrap_content"/>
    <TextView
        tools:text="Rp10000000000000000000000000000"
        android:layout_marginLeft="12dp"
        android:textSize="14dp"
        android:textColor="#F0463C"
        android:fontFamily="@font/roboto_bold"
        android:id="@+id/tv_pay_await_amount"
        app:layout_constraintTop_toTopOf="@id/tv_pay_await_status"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintLeft_toRightOf="@id/tv_pay_await_status"
        android:layout_width="wrap_content"
        android:layout_marginBottom="2dp"
        android:layout_height="wrap_content"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

### 参考

[万字长文 - 史上最全ConstraintLayout（约束布局）使用详解](https://juejin.cn/post/6949186887609221133)

[ConstraintLayout 实现LinearLayout weight效果](https://www.codenong.com/cs107108089/)