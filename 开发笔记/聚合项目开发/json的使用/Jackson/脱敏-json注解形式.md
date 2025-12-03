> 数据脱敏使用


```java
import com.fasterxml.jackson.core.JsonGenerator;  
import com.fasterxml.jackson.databind.BeanProperty;  
import com.fasterxml.jackson.databind.JsonMappingException;  
import com.fasterxml.jackson.databind.JsonSerializer;  
import com.fasterxml.jackson.databind.SerializerProvider;  
import com.fasterxml.jackson.databind.ser.ContextualSerializer;  
import com.fasterxml.jackson.databind.ser.std.StdSerializer;  
  
import java.io.IOException;  
  
/**  
 * 对于私密字段，在json序列号时，可以进行加密  
 */
 public class SecretIDPropSerializer extends StdSerializer<Long> implements ContextualSerializer {  
  
    public SecretIDPropSerializer() {  
        super(Long.class);  
    }  
  
    @Override  
    public JsonSerializer<?> createContextual(SerializerProvider serializerProvider, BeanProperty beanProperty) throws JsonMappingException {  
        return new SecretIDPropSerializer();  
    }  
  
    @Override  
    public void serialize(Long s, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {  
        if(s == null) {  
            jsonGenerator.writeNull();  
            return;  
        }  
        // 如果 json 配置开启了使用  加密  
        if(JsonSerialContext.useSecretId()) {  
            // 例子：加密方法  
            String str = //加密  
            jsonGenerator.writeString(str);  
        }else {  
            jsonGenerator.writeNumber(s);  
        }  
  
    }  
}


// 实体类

@JsonSerialize(using = SecretIDPropSerializer.class)  
private Long id;

```
