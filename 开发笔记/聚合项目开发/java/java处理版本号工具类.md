> 针对版本号的处理


```java
package com.ljq.common.util;  
  
public class VersionComparisonUtil {  
  
  
    /**  
     * 将版本字符串转换为长数字  
     */  
    public static long versionToLong(String version) {  
        if (version == null || version.isEmpty()) {  
            return 0L;  
        }  
  
        String numericVersion = version.replaceAll("[^0-9.]", "");  
        String[] parts = numericVersion.split("\\.");  
        long result = 0L;  
  
        for (int i = 0; i < parts.length; i++) {  
            int value = 0;  
            try {  
                value = Integer.parseInt(parts[i]);  
            } catch (NumberFormatException e) {  
                // 非数字部分用0替代  
                value = 0;  
            }  
            result += value * (long)Math.pow(1000, parts.length - 1 - i);  
        }  
        return result;  
    }  
  
    /**  
     * 版本号比较  
     *  
     * @param version1 版本号1 格式6.1.2  
     * @param version2 版本号2 格式6.1.2  
     * @return 1 版本号1‘大于’版本号2； -1 版本号1‘小于’版本号2； 0 版本号1‘等于’版本号2  
     */public static int compareVersions(String version1, String version2) {  
        String[] arr1 = version1.split("\\.");  
        String[] arr2 = version2.split("\\.");  
  
        int length = Math.max(arr1.length, arr2.length);  
  
        for (int i = 0; i < length; i++) {  
            int v1 = i < arr1.length ? Integer.parseInt(arr1[i]) : 0;  
            int v2 = i < arr2.length ? Integer.parseInt(arr2[i]) : 0;  
  
            if (v1 < v2) {  
                return -1;  
            } else if (v1 > v2) {  
                return 1;  
            }  
        }  
        return 0;  
    }  
  
    /**  
     * 校验版本号格式是否正确(此方法假设版本号只包含数字和点)  
     *     * @param version 版本号字符串  
     * @return 如果版本号格式正确返回true，否则返回false  
     */    public static boolean isValidVersion(String version) {  
        // 使用正则表达式校验版本号  
        return version.matches("\\d+(\\.\\d+)*");  
    }  
  
    /**  
     * 校验版本号格式是否正确(包括预发布版本标识)  
     *     * @param version 版本号字符串  
     * @return 如果版本号格式正确返回true，否则返回false  
     */    public static boolean isValidVersionWithPreRelease(String version) {  
        // 使用正则表达式校验版本号，包括预发布版本标识  
        return version.matches("\\d+(\\.\\d+)*(?:-[a-zA-Z0-9]+)?");  
    }  
  
    public static void main(String[] args) {  
        System.out.println(versionToLong("1.53.0"));  
        System.out.println(versionToLong("1.53.0-alpha"));  
        System.out.println(versionToLong("1.53.1-alpha.3"));  
    }  
  
}
```
