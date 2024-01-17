## 导包

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-integration</artifactId>
</dependency>
```

## QueueChannel 的简单使用
**配置**
```
@Configuration
@IntegrationComponentScan
public class SpringIntegrationConfig {

    @Bean
    public QueueChannel synDingtalkOrganQueueChannel() {
        return new QueueChannel();
    }

}
```
**发送消息**

```
@Autowired
private QueueChannel synDingtalkOrganQueueChannel;
```

**消费消息**

```

@Component
public class SynDingtalkOrganReceiver {

		@Resource
		private ExecutorService executorService;
		
		@ServiceActivator(inputChannel = "synDingtalkOrganQueueChannel", poller = @Poller)
   		 public void onMessage(Long userId) {
	         executorService.execute(() -> {
	            /**
	             *  处理数据
	           	 * */
	       	  });
    	  }
}
```
