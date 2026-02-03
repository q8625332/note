## 前言

> 解放使用 new map的苦恼。可以使用全新简洁的形式写offset

## 代码


```java
package com.ljq.common.utils;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.google.common.collect.Maps;
import org.apache.commons.lang3.StringUtils;

import javax.crypto.Cipher;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import java.nio.charset.StandardCharsets;
import java.util.Base64;
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

/*
* OffsetUtil用法
* 单个：
* OffsetUtil.generateOffset("id",12313131);
* OffsetUtil.parseOffset("offset","id",Long.class);
*
* Fluent API 基础用法:
* Long id = OffsetUtil.parse(offset).getAsLong("id");
* String name = OffsetUtil.parse(offset).getAsString("name");
*
* 链式调用（with可选）
* Double score = OffsetUtil.parse(offset)
*   .with("score", Double.class)
*   .getAsDouble("score");
*
* 多个：
* OffsetParser parser = OffsetUtil.parse(offset);
* Long userId = parser.getAsLong("userId");
* String role = parser.getAsString("role");
* Boolean isActive = parser.getAsBoolean("active");
*
* 获取原始Map
* Map<String, Object> allParams = OffsetUtil.parse(offset).asMap();
*
* 多键解析（兼容原有方法）
* Map<String, Object> result = OffsetUtil.parse(offset)
*     .parseMultiKeys("id", Long.class, "name", String.class);
*
* */
public class OffsetUtil {
    private static final String AES_KEY = "1234567890abcdef"; // 16字节密钥
    private static final String AES_IV = "abcdef1234567890"; // 16字节IV

    /* ---------- 生成相关方法 ---------- */

    public static String generateOffset(String key, Object value) {
        Map<String, Object> map = Maps.newHashMap();
        map.put(key, value);
        return generateOffset(map);
    }

    public static String generateOffset(Map<String, Object> params) {
        Objects.requireNonNull(params, "Offset params cannot be null");
        return encrypt(JSON.toJSONString(params));
    }

    public static OffsetBuilder builder() {
        return new OffsetBuilder();
    }

    /* ---------- 解析相关方法 ---------- */

    /**
     * 创建Fluent API解析器
     * @param offset 加密的offset字符串
     * @return OffsetParser实例
     */
    public static OffsetParser parse(String offset) {
        return new OffsetParser(offset);
    }

    public static <T> T parseOffset(String offset, String key, Class<T> clazz) {
        return parse(offset).get(key, clazz);
    }

    public static Map<String, Object> parseOffset(String offset) {
        return parse(offset).asMap();
    }

    public static Map<String, Object> parseMultiKeys(String offset, Object... keyClassPairs) {
        return parse(offset).parseMultiKeys(keyClassPairs);
    }

    /* ---------- 内部类 ---------- */

    public static class OffsetBuilder {
        private final Map<String, Object> params = Maps.newHashMap();
		
		public OffsetBuilder add(boolean ifAdd, String key, Object value) {  
		    if (!ifAdd) return this;  
		    params.put(key, value);  
		    return this;  
		}
		
        public OffsetBuilder add(String key, Object value) {
            params.put(key, value);
            return this;
        }

        public String build() {
            return generateOffset(params);
        }
    }

    public static class OffsetParser {
        private final Map<String, Object> offsetMap;

        public OffsetParser(String offset) {
            this.offsetMap = StringUtils.isBlank(offset)
                    ? new HashMap<>()
                    : JSON.parseObject(decrypt(offset), Map.class);
        }

        /**
         * Fluent API方式添加要解析的键
         */
        public OffsetParser with(String key, Class<?> clazz) {
            // 保持链式调用，实际解析在get方法中完成
            return this;
        }

        /**
         * 通用获取方法
         */
        public <T> T get(String key, Class<T> clazz) {
            Object value = offsetMap.get(key);
            return convertValue(value, clazz);
        }

        // 类型专用方法
        public Long getAsLong(String key) {
            return get(key, Long.class);
        }

        public Integer getAsInteger(String key) {
            return get(key, Integer.class);
        }

        public String getAsString(String key) {
            return get(key, String.class);
        }

        public Boolean getAsBoolean(String key) {
            return get(key, Boolean.class);
        }

        public Double getAsDouble(String key) {
            return get(key, Double.class);
        }

        public Float getAsFloat(String key) {
            return get(key, Float.class);
        }

        /**
         * 获取原始Map
         */
        public Map<String, Object> asMap() {
            return new HashMap<>(offsetMap);
        }

        /**
         * 多键解析
         */
        public Map<String, Object> parseMultiKeys(Object... keyClassPairs) {
            if (keyClassPairs.length % 2 != 0) {
                throw new IllegalArgumentException("Key and class must be in pairs");
            }

            Map<String, Object> result = new HashMap<>();
            for (int i = 0; i < keyClassPairs.length; i += 2) {
                String key = (String) keyClassPairs[i];
                @SuppressWarnings("unchecked")
                Class<?> clazz = (Class<?>) keyClassPairs[i + 1];
                result.put(key, get(key, clazz));
            }
            return result;
        }
    }

    /* ---------- 私有方法 ---------- */
    // ConvertUtils 这个方法是apache common的工具类
	private static <T> T convertValue(Object value, Class<T> clazz) {  
		if (value == null) return null;  
		try {  
			return clazz.cast(ConvertUtils.convert(value, clazz));  
		} catch (Exception e) {  
			throw new IllegalArgumentException(  
					String.format("Cannot convert '%s' to %s", value, clazz.getSimpleName()), e);  
		}  
	}

    /* ---------- AES加密解密方法 ---------- */
    
    private static String encrypt(String input) {
        try {
            IvParameterSpec iv = new IvParameterSpec(AES_IV.getBytes(StandardCharsets.UTF_8));
            SecretKeySpec keySpec = new SecretKeySpec(AES_KEY.getBytes(StandardCharsets.UTF_8), "AES");

            Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5PADDING");
            cipher.init(Cipher.ENCRYPT_MODE, keySpec, iv);

            byte[] encrypted = cipher.doFinal(input.getBytes());
            return Base64.getEncoder().encodeToString(encrypted);
        } catch (Exception ex) {
            throw new RuntimeException("Encryption failed", ex);
        }
    }

    private static String decrypt(String input) {
        try {
            IvParameterSpec iv = new IvParameterSpec(AES_IV.getBytes(StandardCharsets.UTF_8));
            SecretKeySpec keySpec = new SecretKeySpec(AES_KEY.getBytes(StandardCharsets.UTF_8), "AES");

            Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5PADDING");
            cipher.init(Cipher.DECRYPT_MODE, keySpec, iv);

            byte[] decoded = Base64.getDecoder().decode(input);
            byte[] decrypted = cipher.doFinal(decoded);
            return new String(decrypted);
        } catch (Exception ex) {
            throw new RuntimeException("Decryption failed", ex);
        }
    }
}
```
