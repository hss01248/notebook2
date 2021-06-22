

# 输入框的下拉联想列表pop实现

> 基于ListPopupWindow. 实现popupWindow不被挤开,悬浮于软键盘之上的效果

```java
editText.addTextChangedListener(new TextWatcher(){
            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                super.onTextChanged(s, start, before, count);
                String str = s.toString();
                if(pop != null){
                    pop.dismiss();
                    pop = null;
                }
                if(str.endsWith("@")){
                    showPopEmail(str);
                }
            }
        });

```



```java
 ListPopupWindow pop;
    private void showPopEmail(String str) {
        ArrayList<String> mArrayList=new ArrayList<String>();
        mArrayList.add("@gmail.com");
        mArrayList.add("@qq.com");
        mArrayList.add("@163.com");
        mArrayList.add("@hotmail.com");
        mArrayList.add("@yahoo.com");
        mArrayList.add("@77.net");
        mArrayList.add("@iooo.com");
        mArrayList.add("@xx.com");
        mArrayList.add("@yy.com");
        mArrayList.add("@77.com");
         pop = new ListPopupWindow(getContext());
        ListPopupWindowAdapter adapter = new ListPopupWindowAdapter(mArrayList,getContext());
        pop.setAdapter(adapter);

        pop.setWidth(editText.getMeasuredWidth());
        pop.setHeight(LayoutParams.WRAP_CONTENT);
        pop.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> arg0, View arg1, int position, long arg3) {
                String chosen = mArrayList.get(position);
                AkuToast.debug(chosen);
                String s = str+chosen.substring(1);
                mClearHideEditText.setText(s);
                mClearHideEditText.setSelection(s.length());
                if(pop != null){
                    pop.dismiss();
                }
            }
        });
        pop.setAnchorView(mClearHideEditText);
        pop.setDropDownGravity(Gravity.BOTTOM);
      //关键点1: 设置popupwindow的软键盘模式
        pop.setInputMethodMode(PopupWindow.INPUT_METHOD_NOT_NEEDED);
        pop.setHeight(WindowManager.LayoutParams.WRAP_CONTENT); //设置高度自适应！


        pop.show();
        if(pop.getListView() != null){
            pop.getListView().setBackgroundResource(R.drawable.bg_edit_shape);
            pop.getListView().setVerticalScrollBarEnabled(false);
        }
        Activity activity = ContextUtil.getActivityFromContext(getContext());
        if(activity == null || activity.getWindow() ==null){
            return;
        }
        //关键点2: 设置activity的软键盘模式
    activity.getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_NOTHING);


    }



static Activity getActivityFromContext(Context context) {
        if (context == null) {
            return null;
        }
        if (context instanceof Activity) {
            return (Activity) context;
        }
        while (context instanceof ContextWrapper) {
            if (context instanceof Activity) {
                return (Activity) context;
            }
            context = ((ContextWrapper) context).getBaseContext();
        }
        return null;
    }
```

