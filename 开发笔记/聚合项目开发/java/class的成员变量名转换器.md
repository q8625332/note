> 可以通返回获取对应的名字


```java
package com.ljq.util;  
  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
  
import java.io.Serializable;  
import java.lang.reflect.Method;  
  
/**  
 * 字段名称转换器  
 *  
 * 由于项目中大量使用了:  
 * Example example = new Example(类.class);  
 * example.createCriteria().andEqualTo("userName"); * 要是调整字段就要全家找就很麻烦，所以这里定义了字段名称转换器。替换字段名的时候重置一下类的字段名就行。  
 *  
 * @author ljq  
 * @date 2025/12/26  
 */public class FieldNameConverterUtil {  
  
    private static final Logger log = LoggerFactory.getLogger(FieldNameConverterUtil.class);  
  
  
    @FunctionalInterface  
    public interface SerializableFunction<T, R> extends Serializable {  
        R apply(T t);  
    }  
  
	// 获取下划线名字
    public static <T, R> String toSnakeCase(SerializableFunction<T, R> getter) {  
        String fieldName = getFieldName(getter);  
        return fieldName.replaceAll("([a-z0-9])([A-Z])", "$1_$2").toLowerCase();  
    }  
    
    // 获取驼峰的名字
    public static <T, R> String toCamelCase(SerializableFunction<T, R> getter) {  
        return getFieldName(getter);  
    }  
  
    private static <T, R> String getFieldName(SerializableFunction<T, R> getter) {  
        try {  
            Method writeReplace = getter.getClass().getDeclaredMethod("writeReplace");  
            writeReplace.setAccessible(true);  
            Object serializedLambda = writeReplace.invoke(getter);  
  
            Method getImplMethodName = serializedLambda.getClass().getDeclaredMethod("getImplMethodName");  
            getImplMethodName.setAccessible(true);  
            String methodName = (String) getImplMethodName.invoke(serializedLambda);  
  
            return methodName.startsWith("get") ?  
                    methodName.substring(3, 4).toLowerCase() + methodName.substring(4) :  
                    methodName;  
        } catch (Exception e) {  
            throw new RuntimeException("获取方法名失败", e);  
        }  
    }  
  
  
}
```
