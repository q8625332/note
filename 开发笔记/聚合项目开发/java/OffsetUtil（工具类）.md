## 代码

```java
package com.ljq.common.util;  
  
import com.google.common.collect.Maps;  
import com.hytto.common.util.AESUtilForAppAccount;  
import com.ljq.JsonUtil;  
import org.apache.commons.lang3.StringUtils;  
  
import java.util.HashMap;  
import java.util.Map;  
import java.util.Objects;  
  
public class OffsetUtil {  
  
    /**  
     * 生成加密的 offset 字符串（单键值对）  
     * @param key 键名  
     * @param value 键值  
     * @return 加密后的 offset 字符串  
     */  
    public static String generateOffset(String key, Object value) {  
        Map<String, Object> map = Maps.newHashMap();  
        map.put(key, value);  
        return generateOffset(map);  
    }  
  
    /**  
     * 生成加密的 offset 字符串（多键值对）  
     * @param params 键值对Map  
     * @return 加密后的 offset 字符串  
     */  
    public static String generateOffset(Map<String, Object> params) {  
        Objects.requireNonNull(params, "Offset params cannot be null");  
        return AESUtilForAppAccount.encrypt(JsonUtil.toJson(params));  
    }  
  
    /**  
     * 生成加密的 offset 字符串（Builder模式）  
     * @return OffsetBuilder 实例  
     */  
    public static OffsetBuilder builder() {  
        return new OffsetBuilder();  
    }  
  
    /**  
     * 从 offset 中解析指定键的值  
     * @param offset 加密的 offset 字符串  
     * @param key 要获取的键名  
     * @param clazz 返回值类型  
     * @return 键对应的值  
     */  
    public static <T> T parseOffset(String offset, String key, Class<T> clazz) {  
        Map<String, Object> offsetMap = parseOffset(offset);  
        return clazz.cast(offsetMap.get(key));  
    }  
  
    /**  
     * 从 offset 中解析整个 Map  
     * @param offset 加密的 offset 字符串  
     * @return 包含所有键值对的 Map  
     */    public static Map<String, Object> parseOffset(String offset) {  
        if (StringUtils.isBlank(offset)) {  
            return new HashMap<>();  
        }  
        return JsonUtil.jsonToMap(AESUtilForAppAccount.decrypt(offset));  
    }  
  
    /**  
     * Offset 构建器（支持链式调用）  
     */  
    public static class OffsetBuilder {  
        private final Map<String, Object> params = Maps.newHashMap();  
  
        public OffsetBuilder add(String key, Object value) {  
            params.put(key, value);  
            return this;        }  
  
        public String build() {  
            return generateOffset(params);  
        }  
    }  
  
    /**  
     * 从 offset 中解析多个键值（类型安全）  
     * @param offset 加密的 offset 字符串  
     * @param keyClassPairs 键名和类型的可变参数数组（key1, class1, key2, class2...）  
     * @return 包含解析结果的 Map  
     * @throws IllegalArgumentException 如果参数不是成对出现  
     */  
    public static Map<String, Object> parseMultiKeys(String offset, Object... keyClassPairs) {  
        if (keyClassPairs.length % 2 != 0) {  
            throw new IllegalArgumentException("Key and class must be in pairs");  
        }  
  
        Map<String, Object> result = new HashMap<>();  
        Map<String, Object> offsetMap = parseOffset(offset);  
  
        for (int i = 0; i < keyClassPairs.length; i += 2) {  
            String key = (String) keyClassPairs[i];  
            @SuppressWarnings("unchecked")  
            Class<?> clazz = (Class<?>) keyClassPairs[i + 1];  
  
            if (offsetMap.containsKey(key)) {  
                result.put(key, convertValue(offsetMap.get(key), clazz));  
            }  
        }  
  
        return result;  
    }  
  
    /**  
     * 类型安全的值转换  
     */  
    private static Object convertValue(Object value, Class<?> clazz) {  
        if (value == null) return null;  
  
        if (clazz == Long.class) {  
            return value instanceof Long ? (Long) value : Long.parseLong(value.toString());  
        } else if (clazz == Integer.class) {  
            return value instanceof Integer ? (Integer) value : Integer.parseInt(value.toString());  
        } else if (clazz == String.class) {  
            return value.toString();  
        } else if (clazz == Boolean.class) {  
            return value instanceof Boolean ? (Boolean) value : Boolean.parseBoolean(value.toString());  
        }  
        // 可以继续添加其他类型的支持  
  
        return value;  
    }  
}
```
