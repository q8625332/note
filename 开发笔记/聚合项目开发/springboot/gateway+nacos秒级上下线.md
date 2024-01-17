## 官方的监听没有效果，就不贴官方的代码了

> 创建nacos服务变更监听任务

```java
package com.ljq.gateway.configuration;

import biz.maiye.saas.platform.gateway.event.ServiceEventListener;
import com.alibaba.cloud.nacos.NacosDiscoveryProperties;
import com.alibaba.cloud.nacos.discovery.NacosServiceDiscovery;
import com.alibaba.nacos.api.naming.NamingService;
import jakarta.annotation.Resource;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.util.ArrayList;
import java.util.List;

@Component
@Slf4j
public class ServiceListenerComponent {

    @Resource
    private NacosDiscoveryProperties discoveryProperties;
    @Resource
    private NacosServiceDiscovery serviceDiscovery;
    @Resource
    private ServiceEventListener eventListener;

    @Value("${my.nacos.service.refreshTime:2000}")
    private long refreshTime;

    private volatile NamingService naming;
    protected static volatile List<String> services = new ArrayList<>();

    @Autowired
    public void init() {
        try {
            naming = discoveryProperties.namingServiceInstance();
            services = serviceDiscovery.getServices();
            services.forEach(this::addServiceListener);
            new Thread(() -> {
                for (; ; ) {
                    try {
                        Thread.sleep(refreshTime);
                        List<String> newServices = serviceDiscovery.getServices();
                        for (String service : newServices) {
                            if (!services.contains(service)) {
                                services.add(service);
                                addServiceListener(service);
                            }
                        }
                    } catch (Exception e) {
                        log.error("发生错误！", e);
                    }
                }
            }).start();
        } catch (Exception e) {
            log.error("发生错误！", e);
        }

    }

    /**
     * 新的服务添加监听
     *
     * @param serviceName
     */
    public void addServiceListener(String serviceName) {
        try {
            naming.subscribe(serviceName, eventListener);
            log.info("服务[{}]添加监听", serviceName);
        } catch (Exception e) {
            log.error("发生错误！", e);
        }
    }

}

```
## 创建监听事件，用于处理nacos服务变更后任务处理

```java
package com.ljq.gateway.gateway.event;

import com.alibaba.nacos.api.naming.listener.Event;
import com.alibaba.nacos.api.naming.listener.EventListener;
import com.alibaba.nacos.api.naming.listener.NamingEvent;
import jakarta.annotation.Resource;
import lombok.extern.slf4j.Slf4j;
import org.springframework.cache.Cache;
import org.springframework.cache.CacheManager;
import org.springframework.cloud.context.named.NamedContextFactory;
import org.springframework.stereotype.Component;

import static org.springframework.cloud.loadbalancer.core.CachingServiceInstanceListSupplier.SERVICE_INSTANCE_CACHE_NAME;

@Component
@Slf4j
public class ServiceEventListener implements EventListener {

    @Resource
    private NamedContextFactory factory;

    @Resource
    private CacheManager defaultLoadBalancerCacheManager;

    @Override
    public void onEvent(Event event) {
        if (event instanceof NamingEvent) {
            //销毁服务上下文
            factory.destroy();
            Cache cache = defaultLoadBalancerCacheManager.getCache(SERVICE_INSTANCE_CACHE_NAME);
            if (cache != null) {
                cache.evict(((NamingEvent) event).getServiceName());
                log.info("Spring Gateway 接收实例刷新事件：{}, 开始刷新缓存", ((NamingEvent) event).getServiceName());
            }
            log.info("Spring Gateway 实例刷新完成");
        }
    }

}

```
