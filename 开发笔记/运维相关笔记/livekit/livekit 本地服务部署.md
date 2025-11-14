## csdn有比较详细的例子
[livekit 简单上手教程-CSDN博客](https://blog.csdn.net/qq_21602341/article/details/127793608)
#### 拉取生成文件镜像

```
docker pull livekit/generate
```
#### 生成配置文件

```
docker run --rm -v $PWD:/output livekit/generate --local

输出（本地部署）：
Generated livekit.yaml that's suitable for local testing

Start LiveKit with:
docker run --rm \
    -p 7880:7880 \
    -p 7881:7881 \
    -p 7882:7882/udp \
    -v $PWD/livekit.yaml:/livekit.yaml \
    livekit/livekit-server \
    --config /livekit.yaml \
    --node-ip=127.0.0.1

Note: --node-ip needs to be reachable by the client. 127.0.0.1 is accessible only to the current machine

Server URL:  ws://localhost:7880
API Key: APIoWyzeFdn3WQh
API Secret: dlLp8hWd47e7MmeVCIt7O1XdmmHdjpHl9APteBIX9v0B

Here's a test token generated with your keys: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3OTkwMTM2MzMsImlzcyI6IkFQSW9XeXplRmRuM1dRaCIsIm5hbWUiOiJUZXN0IFVzZXIiLCJuYmYiOjE3NjMwMTM2MzMsInN1YiI6InRlc3QtdXNlciIsInZpZGVvIjp7InJvb20iOiJteS1maXJzdC1yb29tIiwicm9vbUpvaW4iOnRydWV9fQ.TOHiLxYzrtVefWRpuhDnvZG1kunmwpSIlPuW02ULnUU

An access token identifies the participant as well as the room it's connecting to

(记住输出信息，因为运行和对接livekit时候要用)
```
#### 拉取livekit/livekit-server

```
sudo docker pull livekit/livekit-server
```
#### 开始运行服务

```
docker run -d -p 7880:7880 -p 7881:7881 -p 7882:7882/udp -v $PWD/livekit.yaml:/livekit.yaml livekit/livekit-server --config /livekit.yaml --node-ip=127.0.0.1
```

livekit自带了一个事例程序供本地调试使用 [测试地址](https://example.livekit.io/)


```
输入地址：
ws://localhost:7880
输入密钥
密钥
```

## java：

**包：**

```
<!-- livekit-server -->  
<dependency>  
    <groupId>io.livekit</groupId>  
    <artifactId>livekit-server</artifactId>  
    <version>0.10.1</version>  
</dependency>
```

**示例：**

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
list.add("screen_share"); //屏幕共享  
list.add("screen_share_audio"); //屏幕共享音频  
CanPublishSources canPublishSources = new CanPublishSources(list);  
// 配置解释：https://docs.livekit.io/realtime/concepts/authentication/  
token.addGrants(new RoomJoin(true), new RoomName(roomName), canPublishSources);  

System.out.println(token.toJwt());
```
