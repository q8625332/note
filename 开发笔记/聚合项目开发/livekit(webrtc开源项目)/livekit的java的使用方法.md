[测试地址，就是官方的demo](https://example.livekit.io/ "测试地址，就是官方的demo")

## 代码


```
// 用户ID  
String roomName = "ljq-room-01";  
String apiKey = "APIXuaBFdUahpiN";  
String apiSecret = "HhfZgsuVZPNihGBoxhssaJ6231Z7movlFQ5qCwCaNaC";  
AccessToken token = new AccessToken(apiKey, apiSecret);  
String userName = "ljq";  
String userId = "ljq";  
token.setName(userName);  
token.setIdentity(userId);  
token.setMetadata("metadata");  
// 令牌的过期时间（可能）  
token.setExpiration(new Date(System.currentTimeMillis() + 1000L * 60 * 60 * 24 * 365));  
// token所拥有的权限  
List<String> list = new ArrayList<>();  
// 摄像头  
list.add("camera");  
// 麦克风  
list.add("microphone");  
list.add("screen_share"); // 屏幕共享  
list.add("screen_share_audio"); // 屏幕共享音频  
CanPublishSources canPublishSources = new CanPublishSources(list);  
// 配置解释：https://docs.livekit.io/realtime/concepts/authentication/  
token.addGrants(new RoomJoin(true),  
        new RoomName(roomName),  
        new CanSubscribe(true),  
        new CanPublish(true),  
        new CanPublishData(true),  
        canPublishSources);  
System.out.println(token.toJwt());
```

**解释：**

[官方文档](https://docs.livekit.io/realtime/concepts/authentication/ "官方文档")

> 如果只需用户可以看，而不能发语音，视频，文字。
> 可以将：new CanPublish(false) ，new CanPublishData(false)。便可以达到目的。

| 字段                          | 描述       |
| --------------------------- | -------- |
| new RoomJoin(true)          | 是否加入群聊   |
| new CanSubscribe(true)      | 是否可以订阅   |
| new CanPublish(true)        | 是否可以发布   |
| new CanPublishData(true)    | 是否可以发布数据 |
| new CanPublishSources(list) | 发布的相关权限  |
