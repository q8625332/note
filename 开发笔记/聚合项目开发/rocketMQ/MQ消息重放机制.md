>例子：比如业务的MQ消息消费失败了，但是消息又不能丢失。如何让消息不能丢失呢。
>可以使用MQ消息的重放机制，达到让消息不能丢失的效果

**代码如下**

```java
import org.apache.rocketmq.spring.annotation.RocketMQMessageListener;
import org.apache.rocketmq.common.message.MessageExt;
import org.apache.rocketmq.spring.core.RocketMQListener;
import org.apache.rocketmq.client.consumer.listener.ConsumeOrderlyStatus;
import org.apache.rocketmq.client.consumer.listener.ConsumeOrderlyContext;
import org.springframework.stereotype.Component;

@Component
@RocketMQMessageListener(topic = "test-topic", consumerGroup = "test-group")
public class SpecificTagMessageProcessor implements RocketMQListener<MessageExt> {

    @Override
    public ConsumeOrderlyStatus onMessage(MessageExt message) {
        if("test-tag".equals(message.getTags())) { // 指定要处理的 Tag 为 "test-tag"
            try {
                // 处理消息的业务逻辑
                String messageBody = new String(message.getBody());
                System.out.println("Received message with Tag 'test-tag': " + messageBody);
                // 模拟消息处理异常
                if (messageBody.equals("exception")) {
                    throw new RuntimeException("Simulated exception");
                }
            } catch (Exception e) {
                // 记录异常日志
                System.err.println("Exception caught: " + e.getMessage());
                // 返回RECONSUME_LATER表示消息重新消费
                return ConsumeOrderlyStatus.SUSPEND_CURRENT_QUEUE_A_MOMENT;
            }
        }
        // 返回SUCCESS表示消息消费成功
        return ConsumeOrderlyStatus.SUCCESS;
    }
}
```
