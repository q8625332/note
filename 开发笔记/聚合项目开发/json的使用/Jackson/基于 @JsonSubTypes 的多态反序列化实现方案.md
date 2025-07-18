
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
public class BaseParam {
    private String fromUserId;

    public String getFromUserId() {
        return fromUserId;
    }

    public void setFromUserId(String fromUserId) {
        this.fromUserId = fromUserId;
    }
}
```

这里我们使用了：

- `@JsonTypeInfo` 指定使用 `name` 来标识类型，并以 `"paramType"` 为字段名；
- `@JsonSubTypes` 注册所有可能的子类及其对应的类型名。

### `CommentParam.java`（子类示例）


```java
public class CommentParam extends BaseParam {
    private String commentId;
    private String patternId;

    public String getCommentId() {
        return commentId;
    }

    public void setCommentId(String commentId) {
        this.commentId = commentId;
    }

    public String getPatternId() {
        return patternId;
    }

    public void setPatternId(String patternId) {
        this.patternId = patternId;
    }
}
```


### `BaseData.java`（泛型包装器）


```java
import com.fasterxml.jackson.annotation.JsonTypeInfo;

public class BaseData<T> {

    @JsonTypeInfo(
        use = JsonTypeInfo.Id.NAME,
        include = JsonTypeInfo.As.PROPERTY,
        property = "dataType",
        defaultImpl = BaseParam.class
    )
    private T value;

    public BaseData() {}

    public BaseData(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}
```


### 示例 DTO 类：`RemotePatternCommunityActionChangeDTO.java`


```java
public class RemotePatternCommunityActionChangeDTO {

    private BaseData<? extends BaseParam> data;
    private TypeDTO type;
    private ActionDTO action;

    // Getters and Setters
}
```


## ✅ 测试代码


```java
public static void main(String[] args) throws Exception {
    // 构造数据
    CommentParam param = new CommentParam();
    param.setFromUserId("user123");
    param.setPatternId("pattern456");
    param.setCommentId("comment789");

    BaseData<CommentParam> baseData = new BaseData<>(param);

    RemotePatternCommunityActionChangeDTO dto = new RemotePatternCommunityActionChangeDTO();
    dto.setData(baseData);
    dto.setType(RemotePatternCommunityActionChangeDTO.TypeDTO.comment);
    dto.setAction(RemotePatternCommunityActionChangeDTO.ActionDTO.add);

    // 序列化
    String json = JsonUtil.toJson(dto);
    System.out.println("序列化结果:");
    System.out.println(json);

    // 反序列化
    RemotePatternCommunityActionChangeDTO bean = JsonUtil.toBean(json, RemotePatternCommunityActionChangeDTO.class);
    System.out.println("\n反序列化结果:");
    System.out.println(JsonUtil.toJson(bean));
}
```


## ✅ 输出示例


```java
{
  "data": {
    "value": {
      "paramType": "COMMENT",
      "fromUserId": "user123",
      "patternId": "pattern456",
      "commentId": "comment789"
    },
    "dataType": "COMMENT"
  },
  "type": "comment",
  "action": "add"
}
```
