# Google Apis

## 谷歌产品

基本上每个产品都有丰富的api, 很多直接用http直接访问,通过key={key}来控制权限. key在google cloud console上配置.

![image](https://cdn.jsdelivr.net/gh/hss01248/picbed/pic/pic/image-20200602224018316.png)

分三大产品线:  开发,增长,营收



## 谷歌云

https://cloud.google.com/apis

## 谷歌表格 api

用于读写表格

 https://developers.google.com/sheets/api/guides/values

## 谷歌分析 measure protocol :

用于自定义统计维度

 https://developers.google.com/analytics/devguides/collection/protocol/v1/devguide



# jenkins

任意一个界面url加上/api/json即可,

比如: http://<servername>/job/xxx/api/json

通过apiToken访问: 添加http头部

Authorization: Basic base64(userId:apiToken)



# gitlab

```
https://<servername>/api/v4/projects/<projectId>/repository/files/<文件名>/raw?ref=<分支名>&private_token=<token>
```

# nexus

https://help.sonatype.com/repomanager3/rest-and-integration-api



