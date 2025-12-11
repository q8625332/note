## 使用的jar包：

```
<!-- uuid-creator -->
<dependency>
    <groupId>com.github.f4b6a3</groupId>
    <artifactId>uuid-creator</artifactId>
    <version>6.1.1</version>
</dependency>
```

## 测试代码：

```
private static void testUUIDV7() {
        System.out.println("========================================");
        System.out.println("UUID v7 单元测试开始");
        System.out.println("========================================\n");
        
        // 测试报告统计
        int totalTests = 0;
        int passedTests = 0;
        int failedTests = 0;
        StringBuilder reportBuilder = new StringBuilder();
        
        // 测试1: UUID v7 基本生成测试
        totalTests++;
        try {
            System.out.println("【测试1】UUID v7 基本生成测试");
            UUID uuid = UuidCreator.getTimeOrderedEpoch();
            System.out.println("生成的UUID v7: " + uuid);
            System.out.println("UUID版本: " + uuid.version());
            
            if (uuid != null && uuid.version() == 7) {
                System.out.println("✓ 测试通过: UUID v7 生成成功\n");
                passedTests++;
                reportBuilder.append("✓ 测试1: UUID v7 基本生成测试 - 通过\n");
            } else {
                System.out.println("✗ 测试失败: UUID版本不正确\n");
                failedTests++;
                reportBuilder.append("✗ 测试1: UUID v7 基本生成测试 - 失败\n");
            }
        } catch (Exception e) {
            System.out.println("✗ 测试失败: " + e.getMessage() + "\n");
            failedTests++;
            reportBuilder.append("✗ 测试1: UUID v7 基本生成测试 - 异常: " + e.getMessage() + "\n");
        }
        
        // 测试2: UUID v7 时间有序性验证
        totalTests++;
        try {
            System.out.println("【测试2】UUID v7 时间有序性验证");
            int testCount = 1000;
            UUID[] uuids = new UUID[testCount];
            
            // 生成1000个UUID
            for (int i = 0; i < testCount; i++) {
                uuids[i] = UuidCreator.getTimeOrderedEpoch();
                // 添加微小延迟以确保时间戳变化
                if (i % 100 == 0) {
                    Thread.sleep(1);
                }
            }
            
            // 验证有序性
            boolean isOrdered = true;
            int outOfOrderCount = 0;
            for (int i = 1; i < testCount; i++) {
                if (uuids[i].compareTo(uuids[i-1]) < 0) {
                    isOrdered = false;
                    outOfOrderCount++;
                }
            }
            
            System.out.println("生成UUID数量: " + testCount);
            System.out.println("第一个UUID: " + uuids[0]);
            System.out.println("最后一个UUID: " + uuids[testCount-1]);
            System.out.println("乱序数量: " + outOfOrderCount);
            
            if (isOrdered) {
                System.out.println("✓ 测试通过: 所有UUID保持时间有序\n");
                passedTests++;
                reportBuilder.append("✓ 测试2: UUID v7 时间有序性验证 - 通过 (测试" + testCount + "个UUID)\n");
            } else {
                System.out.println("✗ 测试失败: 发现" + outOfOrderCount + "个乱序UUID\n");
                failedTests++;
                reportBuilder.append("✗ 测试2: UUID v7 时间有序性验证 - 失败 (乱序数量: " + outOfOrderCount + ")\n");
            }
        } catch (Exception e) {
            System.out.println("✗ 测试失败: " + e.getMessage() + "\n");
            failedTests++;
            reportBuilder.append("✗ 测试2: UUID v7 时间有序性验证 - 异常: " + e.getMessage() + "\n");
        }
        
        // 测试3: UUID v7 分表测试
        totalTests++;
        try {
            System.out.println("【测试3】UUID v7 分表测试");
            int testCount = 10000;
            Map<String, Integer> tableDistribution = new HashMap<>();
            
            // 模拟分表逻辑：使用UUID的hashCode进行分表
            int tableCount = 16; // 假设分16个表
            
            for (int i = 0; i < testCount; i++) {
                UUID uuid = UuidCreator.getTimeOrderedEpoch();
                // 使用UUID的hashCode进行分表
                int tableIndex = Math.abs(uuid.hashCode()) % tableCount;
                String tableName = "uuid_table_" + tableIndex;
                tableDistribution.put(tableName, tableDistribution.getOrDefault(tableName, 0) + 1);
            }
            
            System.out.println("总UUID数量: " + testCount);
            System.out.println("分表数量: " + tableCount);
            System.out.println("实际使用表数: " + tableDistribution.size());
            System.out.println("\n分表分布情况:");
            
            // 统计分布
            int minCount = Integer.MAX_VALUE;
            int maxCount = Integer.MIN_VALUE;
            for (Map.Entry<String, Integer> entry : tableDistribution.entrySet()) {
                int count = entry.getValue();
                minCount = Math.min(minCount, count);
                maxCount = Math.max(maxCount, count);
                System.out.println("  " + entry.getKey() + ": " + count + " 个UUID");
            }
            
            // 计算分布均匀度（最大值与最小值的比率）
            double distributionRatio = (double) maxCount / minCount;
            System.out.println("\n分布统计:");
            System.out.println("  最小数量: " + minCount);
            System.out.println("  最大数量: " + maxCount);
            System.out.println("  分布比率: " + String.format("%.2f", distributionRatio));
            
            // 判断分布是否合理（比率小于2认为分布较均匀）
            if (tableDistribution.size() == tableCount && distributionRatio < 2.0) {
                System.out.println("✓ 测试通过: UUID分表分布均匀\n");
                passedTests++;
                reportBuilder.append("✓ 测试3: UUID v7 分表测试 - 通过 (分布比率: " + String.format("%.2f", distributionRatio) + ")\n");
            } else if (tableDistribution.size() < tableCount) {
                System.out.println("✗ 测试失败: 未使用所有分表\n");
                failedTests++;
                reportBuilder.append("✗ 测试3: UUID v7 分表测试 - 失败 (仅使用" + tableDistribution.size() + "个表)\n");
            } else {
                System.out.println("⚠ 测试警告: 分布不够均匀，但可接受\n");
                passedTests++;
                reportBuilder.append("⚠ 测试3: UUID v7 分表测试 - 通过但有警告 (分布比率: " + String.format("%.2f", distributionRatio) + ")\n");
            }
        } catch (Exception e) {
            System.out.println("✗ 测试失败: " + e.getMessage() + "\n");
            failedTests++;
            reportBuilder.append("✗ 测试3: UUID v7 分表测试 - 异常: " + e.getMessage() + "\n");
        }
        
        // 测试4: UUID v7 与传统UUID性能对比
        totalTests++;
        try {
            System.out.println("【测试4】UUID v7 与传统UUID性能对比");
            int testCount = 100000;
            
            // 测试UUID v7性能
            long startTime = System.currentTimeMillis();
            for (int i = 0; i < testCount; i++) {
                UuidCreator.getTimeOrderedEpoch();
            }
            long uuidV7Time = System.currentTimeMillis() - startTime;
            
            // 测试传统UUID性能
            startTime = System.currentTimeMillis();
            for (int i = 0; i < testCount; i++) {
                UUID.randomUUID();
            }
            long randomUuidTime = System.currentTimeMillis() - startTime;
            
            System.out.println("生成" + testCount + "个UUID:");
            System.out.println("  UUID v7 耗时: " + uuidV7Time + " ms");
            System.out.println("  Random UUID 耗时: " + randomUuidTime + " ms");
            System.out.println("  性能比率: " + String.format("%.2f", (double) uuidV7Time / randomUuidTime));
            
            System.out.println("✓ 测试通过: 性能测试完成\n");
            passedTests++;
            reportBuilder.append("✓ 测试4: UUID v7 性能测试 - 通过 (v7耗时: " + uuidV7Time + "ms, Random耗时: " + randomUuidTime + "ms)\n");
        } catch (Exception e) {
            System.out.println("✗ 测试失败: " + e.getMessage() + "\n");
            failedTests++;
            reportBuilder.append("✗ 测试4: UUID v7 性能测试 - 异常: " + e.getMessage() + "\n");
        }
        
        // 测试5: UUID v7 唯一性验证
        totalTests++;
        try {
            System.out.println("【测试5】UUID v7 唯一性验证");
            int testCount = 50000;
            Set<UUID> uuidSet = new HashSet<>();
            
            for (int i = 0; i < testCount; i++) {
                UUID uuid = UuidCreator.getTimeOrderedEpoch();
                uuidSet.add(uuid);
            }
            
            System.out.println("生成UUID数量: " + testCount);
            System.out.println("唯一UUID数量: " + uuidSet.size());
            
            if (uuidSet.size() == testCount) {
                System.out.println("✓ 测试通过: 所有UUID唯一\n");
                passedTests++;
                reportBuilder.append("✓ 测试5: UUID v7 唯一性验证 - 通过 (测试" + testCount + "个UUID)\n");
            } else {
                int duplicates = testCount - uuidSet.size();
                System.out.println("✗ 测试失败: 发现" + duplicates + "个重复UUID\n");
                failedTests++;
                reportBuilder.append("✗ 测试5: UUID v7 唯一性验证 - 失败 (重复数量: " + duplicates + ")\n");
            }
        } catch (Exception e) {
            System.out.println("✗ 测试失败: " + e.getMessage() + "\n");
            failedTests++;
            reportBuilder.append("✗ 测试5: UUID v7 唯一性验证 - 异常: " + e.getMessage() + "\n");
        }
        
        // 打印测试报告
        System.out.println("========================================");
        System.out.println("UUID v7 单元测试报告");
        System.out.println("========================================");
        System.out.println("测试时间: " + new java.text.SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
        System.out.println("总测试数: " + totalTests);
        System.out.println("通过数量: " + passedTests);
        System.out.println("失败数量: " + failedTests);
        System.out.println("通过率: " + String.format("%.2f%%", (double) passedTests / totalTests * 100));
        System.out.println("\n详细结果:");
        System.out.println(reportBuilder.toString());
        System.out.println("========================================");
        
        if (failedTests == 0) {
            System.out.println("✓ 所有测试通过！");
        } else {
            System.out.println("✗ 存在" + failedTests + "个测试失败");
        }
        System.out.println("========================================\n");
    }
```

测试结果：

```
========================================
UUID v7 单元测试开始
========================================

【测试1】UUID v7 基本生成测试
生成的UUID v7: 0199f011-a327-708f-adc3-33d597876e5a
UUID版本: 7
✓ 测试通过: UUID v7 生成成功

【测试2】UUID v7 时间有序性验证
生成UUID数量: 1000
第一个UUID: 0199f011-a328-789d-8c61-24b24ab98133
最后一个UUID: 0199f011-a337-7847-833f-180eb53b375f
乱序数量: 0
✓ 测试通过: 所有UUID保持时间有序

【测试3】UUID v7 分表测试
总UUID数量: 10000
分表数量: 16
实际使用表数: 16

分表分布情况:
  uuid_table_12: 570 个UUID
  uuid_table_13: 614 个UUID
  uuid_table_10: 663 个UUID
  uuid_table_11: 625 个UUID
  uuid_table_0: 630 个UUID
  uuid_table_2: 653 个UUID
  uuid_table_1: 596 个UUID
  uuid_table_14: 599 个UUID
  uuid_table_4: 668 个UUID
  uuid_table_15: 608 个UUID
  uuid_table_3: 607 个UUID
  uuid_table_6: 625 个UUID
  uuid_table_5: 657 个UUID
  uuid_table_8: 601 个UUID
  uuid_table_7: 663 个UUID
  uuid_table_9: 621 个UUID

分布统计:
  最小数量: 570
  最大数量: 668
  分布比率: 1.17
✓ 测试通过: UUID分表分布均匀

【测试4】UUID v7 与传统UUID性能对比
生成100000个UUID:
  UUID v7 耗时: 25 ms
  Random UUID 耗时: 21 ms
  性能比率: 1.19
✓ 测试通过: 性能测试完成

【测试5】UUID v7 唯一性验证
生成UUID数量: 50000
唯一UUID数量: 50000
✓ 测试通过: 所有UUID唯一

========================================
UUID v7 单元测试报告
========================================
测试时间: 2025-10-17 10:48:31
总测试数: 5
通过数量: 5
失败数量: 0
通过率: 100.00%

详细结果:
✓ 测试1: UUID v7 基本生成测试 - 通过
✓ 测试2: UUID v7 时间有序性验证 - 通过 (测试1000个UUID)
✓ 测试3: UUID v7 分表测试 - 通过 (分布比率: 1.17)
✓ 测试4: UUID v7 性能测试 - 通过 (v7耗时: 25ms, Random耗时: 21ms)
✓ 测试5: UUID v7 唯一性验证 - 通过 (测试50000个UUID)

========================================
✓ 所有测试通过！
========================================
```

## 测试数据库表：

用于检查排序

```
CREATE TABLE `test_uuid_v7` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT 'id',
  `uuid` varchar(36) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT 'id',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

业务代码

```
package com.ljq.common.test.entity;

import javax.persistence.*;
import java.io.Serializable;

/**
 * @description test_uuid_v7
 * @author ljq
 * @date 2025-10-17
 */
@Table(name="test_uuid_v7")
public class TestUuidV7 implements Serializable {

    private static final long serialVersionUID = 1L;

    @Id
    private Long id;

    /**
     * id
     */
    private String uuid;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUuid() {
        return uuid;
    }

    public void setUuid(String uuid) {
        this.uuid = uuid;
    }
}
```

```
package com.lovense.remote.common.test.dao;

import com.hytto.gfw.dao.base.BaseDao;
import com.lovense.remote.common.test.entity.TestUuidV7;
import org.springframework.stereotype.Repository;

/**
 * @author ljq
 * @description test_uuid_v7
 * @date 2025-10-17
 */
@Repository
public interface TestUuidV7Dao extends BaseDao<TestUuidV7, Long> {


}
```

```
private static void unitTests() {
        final String xml = "/spring/applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(xml);
        context.start();
        CommonSwitcher.ENABLE_USE_ES_SERVER.setStatus(true);

        // 业务代码
        TestUuidV7Dao testUuidV7Dao = SpringContextHolder.getBean(TestUuidV7Dao.class);
        RemoteAsyncDBSourceUtil.executeAsyncWithDataSource(CommonDataSourceTypeConst.REMOTE_DATASOURCE_NAME, () -> {
            new Thread(() -> {
                for (int i = 0; i < 100; i++) {
                    TestUuidV7 testUuidV7 = new TestUuidV7();
                    testUuidV7.setUuid(UuidCreator.getTimeOrderedEpoch().toString().replaceAll("-",""));
                    testUuidV7Dao.insert(testUuidV7);
                }
            }).start();
        });
        System.out.println("==================== done ======================");
    }
```

结论：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/179db66cd1ca447d803530ec1598a2dd.png)