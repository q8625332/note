
### `BaseParam.java`（抽象基类 + 多态支持）


```java
import com.fasterxml.jackson.annotation.JsonSubTypes;
import com.fasterxml.jackson.annotation.JsonTypeInfo;

@JsonTypeInfo(use = JsonTypeInfo.Id.CLASS, include = JsonTypeInfo.As.PROPERTY, property = "@class")
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

    @JsonTypeInfo(use = JsonTypeInfo.Id.CLASS, include = JsonTypeInfo.As.PROPERTY, property = "@class")
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
      "@class": "your.package.CommentParam",
      "fromUserId": "user123",
      "patternId": "pattern456",
      "commentId": "comment789"
    }
  },
  "type": "comment",
  "action": "add"
}
```

## 响应之后不想让用户知道有`@class`的报名如何处理呢?


```
首先需要在 序列化子类加上
@JsonTypeInfo(use = JsonTypeInfo.Id.CLASS, include = JsonTypeInfo.As.PROPERTY, property = "@class")
然后在新建Views实体类，重新制定忽略策略
@JsonView(Views.Internal.class) // 只在内部视图序列化
@JsonProperty("@class")
```

**使用例子：**


```
// 定义视图
public class Views {
    public static class Public {}
    public static class Internal extends Public {}
}

// DTO 类
@JsonTypeInfo(use = JsonTypeInfo.Id.CLASS, include = JsonTypeInfo.As.PROPERTY, property = "@class")
public class ArticleHomeOffsetRespDTO {
    
    @JsonView(Views.Internal.class) // 只在内部视图序列化
    @JsonProperty("@class")
    private String clazz;
    
    private String title;
    // ...
}

```
