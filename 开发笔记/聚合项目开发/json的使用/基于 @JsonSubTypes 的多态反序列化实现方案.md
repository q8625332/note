
### `BaseParam.java`（抽象基类 + 多态支持）


```java
import com.fasterxml.jackson.annotation.JsonSubTypes;
import com.fasterxml.jackson.annotation.JsonTypeInfo;

@JsonTypeInfo(
    use = JsonTypeInfo.Id.NAME,              // 使用名字而不是类名
    include = JsonTypeInfo.As.PROPERTY,     // 作为属性写入 JSON
    property = "paramType"                  // 属性名为 "paramType"
)
@JsonSubTypes({
    @JsonSubTypes.Type(value = CommentParam.class, name = "COMMENT"),
    @JsonSubTypes.Type(value = LikeParam.class, name = "LIKE")
    // 添加更多子类...
})
public abstract class BaseParam {
    private String fromUserId;

    public String getFromUserId() {
        return fromUserId;
    }

    public void setFromUserId(String fromUserId) {
        this.fromUserId = fromUserId;
    }
}
```
