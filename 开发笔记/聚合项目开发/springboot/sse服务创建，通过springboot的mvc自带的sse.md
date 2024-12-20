 **编写核心SSE Client 的service**

>sseEmitterMap 用于储存用户的链接，也方便于通过uid查找对于的客户端链接发送消息

```java
@Slf4j
@Component
public class SseClient {
    private static final Map<String, SseEmitter> sseEmitterMap = new ConcurrentHashMap<>();
    /**
     * 创建连接
     */
    public SseEmitter createSse(String uid) {
        //默认30秒超时,设置为0L则永不超时
        SseEmitter sseEmitter = new SseEmitter(0l);
        //完成后回调
        sseEmitter.onCompletion(() -> {
            log.info("[{}]结束连接...................", uid);
            sseEmitterMap.remove(uid);
        });
        //超时回调
        sseEmitter.onTimeout(() -> {
            log.info("[{}]连接超时...................", uid);
        });
        //异常回调
        sseEmitter.onError(
                throwable -> {
                    try {
                        log.info("[{}]连接异常,{}", uid, throwable.toString());
                        sseEmitter.send(SseEmitter.event()
                                .id(uid)
                                .name("发生异常！")
                                .data("发生异常请重试！")
                                .reconnectTime(3000));
                        sseEmitterMap.put(uid, sseEmitter);
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
        );
        try {
            sseEmitter.send(SseEmitter.event().reconnectTime(5000));
        } catch (IOException e) {
            e.printStackTrace();
        }
        sseEmitterMap.put(uid, sseEmitter);
        log.info("[{}]创建sse连接成功！", uid);
        return sseEmitter;
    }
 
    /**
     * 给指定用户发送消息
     *
     */
    public boolean sendMessage(String uid,String messageId, String message) {
        if (StrUtil.isBlank(message)) {
            log.info("参数异常，msg为null", uid);
            return false;
        }
        SseEmitter sseEmitter = sseEmitterMap.get(uid);
        if (sseEmitter == null) {
            log.info("消息推送失败uid:[{}],没有创建连接，请重试。", uid);
            return false;
        }
        try {
            sseEmitter.send(SseEmitter.event().id(messageId).reconnectTime(1*60*1000L).data(message));
            log.info("用户{},消息id:{},推送成功:{}", uid,messageId, message);
            return true;
        }catch (Exception e) {
            sseEmitterMap.remove(uid);
            log.info("用户{},消息id:{},推送异常:{}", uid,messageId, e.getMessage());
            sseEmitter.complete();
            return false;
        }
    }
 
    /**
     * 断开
     * @param uid
     */
    public void closeSse(String uid){
        if (sseEmitterMap.containsKey(uid)) {
            SseEmitter sseEmitter = sseEmitterMap.get(uid);
            sseEmitter.complete();
            sseEmitterMap.remove(uid);
        }else {
            log.info("用户{} 连接已关闭",uid);
        }
 
    }
 
}
```

**模拟演示，链接和消息推送**

```java
@Controller
public class IndexAction {
    @Autowired
    private SseClient sseClient;
    @GetMapping("/")
    public String index(ModelMap model) {
        String uid = IdUtil.fastUUID();
        model.put("uid",uid);
        return "index";
    }
 
    @CrossOrigin
    @GetMapping("/createSse")
    public SseEmitter createConnect(String uid) {
        return sseClient.createSse(uid);
    }
    @CrossOrigin
    @GetMapping("/sendMsg")
    @ResponseBody
    public String sseChat(String uid) {
        for (int i = 0; i < 10; i++) {
            sseClient.sendMessage(uid, "no"+i,IdUtil.fastUUID());
        }
        return "ok";
    }
 
    /**
     * 关闭连接
     */
    @CrossOrigin
    @GetMapping("/closeSse")
    public void closeConnect(String uid ){
 
        sseClient.closeSse(uid);
    }
}
```

**前端代码**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="con"></div>
<script>
    let chat = document.getElementById("con");
    if (window.EventSource) {
        //创建sse
         eventSource = new EventSource(`/createSse?uid=${uid}`);
        eventSource.onopen = function (event) {
            console.log('SSE链接成功');
        }
        eventSource.onmessage = function (event) {
            if(event.data){
                chat.innerHTML += event.data + '<br/>';
                //console.log('后端返回的数据:', data.value);
            }
        }
        eventSource.onerror = (error) => {
            console.log('SSE链接失败');
        };
    } else {
        alert("你的浏览器不支持SSE");
    }
</script>
</body>
</html>
```