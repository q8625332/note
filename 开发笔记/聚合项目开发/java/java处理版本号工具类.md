> 针对版本号的处理


```java
package com.lovense.remote.common.util;  
  
public class VersionComparisonUtil {  
  
  
    /**  
     * 将版本字符串转换为长数字  
     */  
    public static long versionToLong(String version) {  
        if (version == null || version.isEmpty()) {  
            return 0L;  
        }  
  
        // 先提取数字部分  
        String numericVersion = version.replaceAll("[^0-9.]", "");  
        String[] parts = numericVersion.split("\\.");  
        long result = 0L;  
  
        // 处理所有版本号部分，不限制为3位  
        for (int i = 0; i < parts.length; i++) {  
            int value = 0;  
            try {  
                value = Integer.parseInt(parts[i]);  
            } catch (NumberFormatException e) {  
                // 非数字部分用0替代  
                value = 0;  
            }  
            // 每个部分乘以1000的幂次方,从高位到低位依次递减  
            result += value * (long)Math.pow(1000, parts.length - 1 - i);  
        }  
        return result;  
    }  
  
    public static void main(String[] args) {  
        System.out.println(versionToLong("1.53.0"));  
        System.out.println(versionToLong("1.53.0-alpha"));  
        System.out.println(versionToLong("1.53.1-alpha.3"));  
    }  
  
}
```
