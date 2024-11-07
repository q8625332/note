## CompletableFuture的再次封装

> 由于我们的业务会大量使用到重复性代码，所以封装了一个新工具类来，减少重复造轮子的问题
> CompletableFuture没有全部重写，有需要自己重写封装

```java
package com.ljq.commons.utils;

import cn.hutool.core.map.MapUtil;
import cn.hutool.core.util.ObjUtil;
import cn.hutool.core.util.StrUtil;
import lombok.SneakyThrows;
import org.jetbrains.annotations.NotNull;

import java.util.Map;
import java.util.Optional;
import java.util.UUID;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.function.Function;
import java.util.function.Supplier;

/**
 * CompletableFuture的工具类
 *
 * @author 刘俊秦
 * @date 2023/07/31
 */
public abstract class CompletableFutureUtil {

    private static CompletableFutureUtil createInstance() {
        return new CompletableFutureTaskUtil();
    }

    public static CompletableFutureUtil getInstance() {
        return createInstance();
    }

    private static CompletableFutureUtil createInstance(ExecutorService executorService) {
        return new CompletableFutureTaskUtil(executorService);
    }

    public static CompletableFutureUtil getInstance(ExecutorService executorService) {
        return createInstance(executorService);
    }

    public abstract <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier);

    public abstract <U> CompletableFuture<U> supplyAsync(String key, Supplier<U> supplier);

    public abstract <F, U> CompletableFuture<F> thenApplyAsync(CompletableFuture<U> from, Function<? super U, ? extends F> function);

    public abstract <F, U> CompletableFuture<F> thenApplyAsync(String key, CompletableFuture<U> from, Function<? super U, ? extends F> function);

    public abstract void allOf();

    public abstract Object getMapValueByKey(String key);

    public abstract void shutdown();

}

class CompletableFutureTaskUtil extends CompletableFutureUtil {

    public ExecutorService executorService;

    public Map<String, CompletableFuture<?>> futureMap;

    public CompletableFutureTaskUtil() {
        //初始化futureMap
        generateFutureMap();
    }

    public CompletableFutureTaskUtil(ExecutorService executorService) {
        //赋值线程池
        if (ObjUtil.isNotNull(executorService)) {
            this.executorService = executorService;
        }
        //初始化futureMap
        generateFutureMap();
    }

    private void generateFutureMap() {
        this.futureMap = MapUtil.newConcurrentHashMap();
    }

    public <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {
        CompletableFuture<U> future = this.getCompletableFutureSupplyAsync(supplier);
        futureMap.put(UUID.randomUUID().toString(), future);
        return future;
    }

    public <U> CompletableFuture<U> supplyAsync(String key, Supplier<U> supplier) {
        CompletableFuture<U> future = this.getCompletableFutureSupplyAsync(supplier);
        futureMap.put(Optional.ofNullable(key).filter(StrUtil::isNotBlank).orElse(UUID.randomUUID().toString()), future);
        return future;
    }

    @Override
    public <F, U> CompletableFuture<F> thenApplyAsync(CompletableFuture<U> from, Function<? super U, ? extends F> function) {
        CompletableFuture<F> future = this.getCompletableFutureThenApplyAsync(from, function);
        futureMap.put(UUID.randomUUID().toString(), future);
        return future;
    }

    @Override
    public <F, U> CompletableFuture<F> thenApplyAsync(String key, CompletableFuture<U> from, Function<? super U, ? extends F> function) {
        CompletableFuture<F> future = this.getCompletableFutureThenApplyAsync(from, function);
        futureMap.put(Optional.ofNullable(key).filter(StrUtil::isNotBlank).orElse(UUID.randomUUID().toString()), future);
        return future;
    }

    @NotNull
    private <U> CompletableFuture<U> getCompletableFutureSupplyAsync(Supplier<U> supplier) {
        CompletableFuture<U> future;
        if (ObjUtil.isNotNull(this.executorService)) {
            future = CompletableFuture.supplyAsync(supplier, this.executorService);
        } else {
            future = CompletableFuture.supplyAsync(supplier);
        }
        return future;
    }

    @NotNull
    private <F, U> CompletableFuture<F> getCompletableFutureThenApplyAsync(CompletableFuture<U> from, Function<? super U, ? extends F> function) {
        CompletableFuture<F> future;
        if (ObjUtil.isNotNull(this.executorService)) {
            future = from.thenApplyAsync(function, this.executorService);
        } else {
            future = from.thenApplyAsync(function);
        }
        return future;
    }

    @SneakyThrows
    public void allOf() {
        CompletableFuture.allOf(this.futureMap.values().toArray(new CompletableFuture[0])).get();
    }

    @SneakyThrows
    @Override
    public Object getMapValueByKey(String key) {
        return this.futureMap.get(key).get();
    }

    @Override
    public void shutdown() {
        this.executorService = null;
        this.futureMap = null;
    }

}
```

> 使用效果

```java
CompletableFutureUtil completableFutureUtil = CompletableFutureUtil.getInstance(AsyncConfiguration.myExecutorService);
CompletableFuture<List<Long>> idListCompletableFuture = completableFutureUtil.supplyAsync(() -> page.getRecords().stream().map(Entity::getId).collect(Collectors.toList()));
CompletableFuture<List<SaleOutboundItem>> itemCompletableFuture = completableFutureUtil.thenApplyAsync(idListCompletableFuture, saleOutboundItemService::getDataByOutboundIds);
CompletableFuture<List<Long>> itemIdsCompletableFuture = completableFutureUtil.thenApplyAsync(itemCompletableFuture, list -> Optional.ofNullable(list).filter(CollUtil::isNotEmpty).map(m -> m.stream().map(SaleOutboundItem::getId).collect(Collectors.toList())).orElse(null));
completableFutureUtil.thenApplyAsync(itemCompletableFuture, items -> Optional.ofNullable(items).filter(CollUtil::isNotEmpty).map(m -> m.stream().collect(Collectors.groupingBy(SaleOutboundItem::getOutboundId))).orElse(MapUtil.newHashMap()));
completableFutureUtil.thenApplyAsync("itemBoltMap", itemIdsCompletableFuture, itemIds -> Optional.ofNullable(itemIds).filter(CollUtil::isNotEmpty).map(ids -> Optional.ofNullable(saleOutboundItemBoltService.getDataByOutboundItemIds(ids)).filter(CollUtil::isNotEmpty).map(bolts -> bolts.stream().collect(Collectors.groupingBy(SaleOutboundItemBolt::getOutboundItemId))).orElse(MapUtil.newHashMap())).orElse(MapUtil.newHashMap()));
completableFutureUtil.thenApplyAsync("itemWarehouseLocationMap", itemIdsCompletableFuture, itemIds -> Optional.ofNullable(itemIds).filter(CollUtil::isNotEmpty).map(ids -> Optional.ofNullable(saleOutboundItemWarehouseLocationService.getDataByOutboundItemIds(ids)).filter(CollUtil::isNotEmpty).map(bolts -> bolts.stream().collect(Collectors.groupingBy(SaleOutboundItemWarehouseLocation::getOutboundItemId))).orElse(MapUtil.newHashMap())).orElse(MapUtil.newHashMap()));
completableFutureUtil.allOf();
Map<Long, List<SaleOutboundItem>> itemMap = (Map<Long, List<SaleOutboundItem>>) completableFutureUtil.getMapValueByKey("itemMap");
Map<Long, List<SaleOutboundItemBolt>> itemBoltMap = (Map<Long, List<SaleOutboundItemBolt>>) completableFutureUtil.getMapValueByKey("itemBoltMap");
Map<Long, List<SaleOutboundItemWarehouseLocation>> itemWarehouseLocationMap = (Map<Long, List<SaleOutboundItemWarehouseLocation>>) completableFutureUtil.getMapValueByKey("itemWarehouseLocationMap");
completableFutureUtil.shutdown();
```
