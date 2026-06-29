```markdown
---
name: yapi-doc
description: 自动分析 Java 接口代码，生成标准接口文档，并同步到 YApi 平台的通用技能。适用于基于 Controller、Handler 等形式编写的 Java HTTP 接口文档生成与维护。
metadata:
  author: api-team
  version: 1.0.0
---

## 技能说明

本技能用于分析 Java 接口代码，自动生成接口文档，并同步到私有部署的 YApi 平台。

- YApi 平台地址：由用户提供，例如：`https://yapi.example.com`
- 支持接口类型：
  - Spring MVC / Spring Boot Controller
  - 自定义 Handler
  - 其他可通过注解、常量或方法签名识别 HTTP 接口的 Java 类

## YApi Token 配置

YApi Token 由用户提供。

同步前应优先通过以下接口验证 token 对应的项目信息：

```http
GET {YAPI_HOST}/api/project/get?token={token}
```

需要从返回结果中获取：

- `project_id`
- `basepath`
- 项目名称
- 其他项目配置

## 工作流程

### 第一步：分析代码，提取接口信息

根据用户指定的文件、类、目录或模块，分析接口代码，提取以下信息：

- 接口路径
- 请求方法
- 接口标题
- 接口描述
- 请求头
- 请求参数
- 响应结构
- 接口分类
- 权限要求
- 响应码
- 注意事项

#### 1. 接口路径提取规则

优先从以下位置提取接口路径：

- `@RequestMapping`
- `@PostMapping`
- `@GetMapping`
- `@PutMapping`
- `@DeleteMapping`
- `@PatchMapping`
- 自定义接口注解
- 类或方法中的 URL 常量
- 路由注册代码
- 框架约定路径

如果类上和方法上都存在路径，需要拼接为完整接口路径。

例如：

```java
@RequestMapping("/user")
public class UserController {

    @PostMapping("/detail")
    public Result<UserVO> detail(@RequestBody UserDetailParam param) {
        ...
    }
}
```

接口路径为：

```text
/user/detail
```

#### 2. 请求方法提取规则

优先从注解中识别请求方法：

- `@PostMapping` → `POST`
- `@GetMapping` → `GET`
- `@PutMapping` → `PUT`
- `@DeleteMapping` → `DELETE`
- `@PatchMapping` → `PATCH`
- `@RequestMapping(method = RequestMethod.POST)` → `POST`

如果无法识别，请根据项目约定或用户说明确定。

#### 3. 接口标题提取规则

优先级如下：

1. 方法 Javadoc 第一行
2. 类 Javadoc 第一行
3. 方法名转换后的中文或英文描述
4. 类名转换后的中文或英文描述

#### 4. 接口描述提取规则

接口描述应综合以下信息生成：

- 类 Javadoc
- 方法 Javadoc
- 参数注释
- 业务逻辑
- Service 调用链
- 校验逻辑
- 异常处理
- 权限注解
- 状态流转
- 数据来源

描述不能只写方法名，应说明接口的实际业务用途。

#### 5. 请求参数提取规则

根据接口类型提取请求参数：

##### Spring Controller

- `@RequestBody` 对应的 Param / DTO / Req 类
- `@RequestParam` 参数
- `@PathVariable` 参数
- `@RequestHeader` 参数
- `HttpServletRequest` 中读取的参数
- JSON body 手动解析出的参数

##### 自定义 Handler

- 请求解析方法中的 DTO / Req 类
- JSON body 解析对象
- 参数上下文对象
- 手动读取的 query、body、header 参数

#### 6. 响应结构提取规则

必须沿接口调用链分析真实响应结构：

- Controller / Handler 返回值
- Service 返回值
- Result 包装类
- DTO / VO / Entity
- 分页对象
- 列表元素
- Map 的 value 类型
- RPC / Feign / Dubbo / SDK 返回对象
- 转换器、Builder、BeanUtils、MapStruct、手工 set 逻辑

禁止在未调查清楚时直接写：

```json
{
  "data": {}
}
```

或：

```json
{
  "data": "..."
}
```

如果完整调查后仍无法定位字段定义，才允许在字段说明中标注：

```text
未在当前项目和可访问依赖中找到更具体结构
```

### 第二步：构建 YApi 接口文档数据结构

将提取的信息转换为 YApi OpenAPI 所需的 JSON 格式。

## 关键规则

### 1. 接口路径规则

YApi 项目可能已配置 `basepath`，因此同步到 YApi 的接口路径必须去掉项目 `basepath` 前缀。

例如：

- 项目 `basepath`：`/api`
- 代码中的完整路径：`/api/user/detail`
- 同步到 YApi 的路径：`/user/detail`

错误示例：

```text
/api/user/detail
```

如果 YApi 已配置 `basepath = /api`，则最终访问路径会被拼成：

```text
/api/api/user/detail
```

正确示例：

```text
/user/detail
```

同步前必须通过项目信息确认是否存在 `basepath`。

### 2. 请求参数和响应体必须使用 JSON Schema 格式

YApi 只有在收到标准 JSON Schema 格式时，才会将请求参数和响应体渲染为可视化表格。

必须设置以下字段：

```json
{
  "req_body_type": "json",
  "req_body_is_json_schema": true,
  "res_body_type": "json",
  "res_body_is_json_schema": true
}
```

其中：

- `req_body_other` 必须是 JSON Schema 字符串
- `res_body` 必须是 JSON Schema 字符串

#### JSON Schema 示例

```json
{
  "type": "object",
  "properties": {
    "userId": {
      "type": "string",
      "description": "用户 ID"
    },
    "pageNo": {
      "type": "number",
      "description": "页码"
    },
    "enabled": {
      "type": "boolean",
      "description": "是否启用"
    },
    "items": {
      "type": "array",
      "description": "列表",
      "items": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string",
            "description": "数据 ID"
          }
        }
      }
    }
  },
  "required": ["userId"]
}
```

#### Java 类型到 JSON Schema 类型映射

| Java 类型 | JSON Schema 类型 |
|---|---|
| `String` | `string` |
| `Long` / `Integer` / `int` / `long` / `BigDecimal` / `Double` / `Float` | `number` |
| `Boolean` / `boolean` | `boolean` |
| `List<T>` / `Set<T>` / 数组 | `array` |
| `Map` | `object` |
| 自定义对象 | `object` |
| `Date` / `LocalDateTime` / `LocalDate` | `string` |

时间类型需要在 `description` 中注明格式，例如：

```text
创建时间，格式：yyyy-MM-dd HH:mm:ss
```

或：

```text
创建时间戳，单位毫秒
```

#### DTO 注解到 JSON Schema 映射

| Java 注解 | 处理方式 |
|---|---|
| `@NotNull` | 加入父级 `required` |
| `@NotBlank` | 加入父级 `required` |
| `@NotEmpty` | 加入父级 `required` |
| `@Size` | 写入字段说明 |
| `@Min` | 写入字段说明 |
| `@Max` | 写入字段说明 |
| `@Pattern` | 写入字段说明 |
| 字段 Javadoc / 行注释 | 写入 `description` |

### 3. 备注字段规则

YApi 的备注渲染采用双字段机制：

- `desc`：HTML 格式，供 YApi 页面直接展示
- `markdown`：Markdown 原文，供 YApi 编辑器回显

同步接口时必须同时设置这两个字段。

如果只往 `desc` 写 Markdown 文本，YApi 页面会把 Markdown 标记当普通文本显示，导致标题、表格、列表无法正确渲染。

## 备注内容要求

`markdown` 字段必须包含以下章节：

```markdown
## 功能说明

## 请求头

## 请求参数

## 响应示例

## 响应码

## 权限要求

## 注意事项
```

其中：

- `## 功能说明`：说明接口用途、主要业务行为、关键前置条件。
- `## 请求头`：列出 `Content-Type`、认证头、业务额外读取的 header。
- `## 请求参数`：给出 JSON 示例，并追加请求参数说明表格。
- `## 响应示例`：给出准确响应格式，并追加响应字段说明表格。
- `## 响应码`：列出数字 code、说明、触发场景。
- `## 权限要求`：根据代码中的权限注解、拦截器或配置填写。
- `## 注意事项`：只在有分页、幂等、状态流转、异步处理、缓存延迟等调用风险时填写；没有可省略。

### 请求参数说明表格要求

请求 JSON 示例后必须紧跟：

```markdown
### 请求参数说明

| 参数名 | 类型 | 必填 | 示例值 | 说明 |
|---|---|---|---|---|
```

嵌套对象使用点号路径。

数组元素使用 `[]`。

示例：

```text
items[].id
items[].name
user.profile.avatar
```

### 响应字段说明表格要求

响应 JSON 示例后必须紧跟：

```markdown
### 响应字段说明

| 字段路径 | 类型 | 示例值 | 说明 |
|---|---|---|---|
```

统一响应包装字段、分页字段、列表元素字段、嵌套 DTO / VO 字段都必须递归展开。

### JSON 示例与说明表一致性要求

必须保证：

- JSON 示例中的字段，在说明表中都存在。
- 说明表中的字段，在 JSON 示例中都存在。
- JSON Schema、JSON 示例、说明表三者字段一致。
- 字段类型一致。
- 字段含义一致。
- 示例值符合字段类型。

禁止出现：

- JSON 示例中有字段，表格缺失说明。
- 表格出现 JSON 示例不存在的字段。
- JSON Schema 与备注中的字段不一致。
- 响应示例只写 `{}`。
- 响应示例使用不确定的猜测字段。

### desc HTML 要求

`desc` 字段中的 HTML 也必须包含等价内容。

例如 Markdown 中有：

```markdown
### 请求参数说明

| 参数名 | 类型 | 必填 | 示例值 | 说明 |
|---|---|---|---|---|
| userId | string | 是 | 10001 | 用户 ID |
```

HTML 中也必须有：

```html
<h3>请求参数说明</h3>
<table>
  <thead>
    <tr>
      <th>参数名</th>
      <th>类型</th>
      <th>必填</th>
      <th>示例值</th>
      <th>说明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>userId</td>
      <td>string</td>
      <td>是</td>
      <td>10001</td>
      <td>用户 ID</td>
    </tr>
  </tbody>
</table>
```

### Markdown 到 HTML 转换规则

| Markdown | HTML |
|---|---|
| `## 标题` | `<h2>标题</h2>` |
| `### 标题` | `<h3>标题</h3>` |
| 段落 | `<p>段落</p>` |
| `- 列表项` | `<ul><li>列表项</li></ul>` |
| 表格 | `<table>...</table>` |
| `` `code` `` | `<code>code</code>` |
| 代码块 | `<pre><code>代码内容</code></pre>` |

HTML 中需要对特殊字符转义：

| 字符 | 转义 |
|---|---|
| `"` | `&quot;` |
| `<` | `&lt;` |
| `>` | `&gt;` |
| `&` | `&amp;` |

### 4. 响应示例深挖要求

生成备注前必须沿接口调用链深入调查真实返回结构：

- Controller / Handler 返回值
- Service 返回值
- 统一 Result 包装类
- DTO / VO / Entity
- 分页对象
- 列表元素
- Map value 类型
- 外部接口返回对象
- 转换器
- Builder
- BeanUtils
- MapStruct
- 手工 set 方法

对于嵌套对象，必须递归展开到可说明业务含义的字段层级。

对于列表，必须展开单个元素结构。

对于分页响应，必须展开：

- 当前页
- 每页数量
- 总数
- 是否有下一页
- 游标
- 列表字段
- 列表元素字段

如果响应字段来自依赖包或外部接口定义，应继续查找可访问源码、接口定义或 DTO / VO 类。

只有在完整调查后仍无法定位字段定义时，才允许说明：

```text
未在当前项目和可访问依赖中找到更具体结构
```

禁止直接编造字段。

### 5. 响应码要求

生成备注前必须检查接口相关代码路径：

- Controller / Handler
- Service
- 参数校验逻辑
- 异常抛出点
- 错误码枚举
- 错误码常量类
- 统一异常处理器
- 外部接口可能透传的错误码

如果代码中出现以下形式，必须继续追踪最终数字 code：

- `ResultCode`
- `ErrorCode`
- `BizErrorCode`
- `CommonResultCode`
- `throw new BizException(...)`
- `ResponseResult.fail(...)`
- `Result.fail(...)`

`## 响应码` 中的 code 必须写实际数字。

正确示例：

```markdown
| code | 说明 | 触发场景 |
|---|---|---|
| 0 | 成功 | 请求处理成功 |
| 1001 | 参数校验失败 | 请求体为空或字段缺失 |
| 401 | 未登录或 Token 失效 | 认证头缺失、无效或已过期 |
| 403 | 无权限 | 当前用户没有访问该接口的权限 |
| 500 | 服务器内部错误 | 未预期异常或下游服务异常 |
```

错误示例：

```markdown
| INVALID_PARAM | 参数校验失败 | 参数为空 |
| AUTH_FAILED | 未登录 | token 无效 |
```

如果枚举名有业务含义，可以写到说明中：

```markdown
| 1001 | 参数校验失败（CommonResultCode.INVALID_PARAM） | 请求体为空或字段缺失 |
```

只补充能从当前接口调用链判断会返回的错误码。

无法确认归属的错误码不要编造，可写：

```text
未在当前接口调用链发现明确业务错误码
```

如果项目存在统一错误码约定，也应保留常见通用错误码，但必须写数字 code。

### 6. 权限要求规则

如果代码中存在权限注解、拦截器、配置或方法调用，应将权限信息写入备注。

常见来源包括：

- 权限注解
- 登录校验注解
- 角色注解
- 菜单权限注解
- 按钮权限注解
- 拦截器配置
- 网关权限配置
- 代码中的权限判断逻辑

Markdown 示例：

```markdown
## 权限要求

- 需要登录
- 菜单权限：`USER_MANAGE`
- 按钮权限：`USER_DETAIL`
```

HTML 示例：

```html
<h2>权限要求</h2>
<ul>
<li>需要登录</li>
<li>菜单权限：<code>USER_MANAGE</code></li>
<li>按钮权限：<code>USER_DETAIL</code></li>
</ul>
```

如果接口无权限要求，也要明确说明：

```markdown
## 权限要求

无特殊权限要求。
```

如果接口只需要登录：

```markdown
## 权限要求

需要登录后访问。
```

如果接口明确跳过权限校验：

```markdown
## 权限要求

该接口已配置跳过权限校验。
```

### 7. 默认请求头规则

生成接口文档时，应根据项目实际情况自动补充请求头。

默认至少包含：

```json
[
  {
    "name": "Content-Type",
    "value": "application/json",
    "required": "1"
  }
]
```

如果项目存在统一认证头，应加入对应请求头。

例如：

```json
[
  {
    "name": "Content-Type",
    "value": "application/json",
    "required": "1"
  },
  {
    "name": "Authorization",
    "value": "",
    "required": "1"
  }
]
```

常见认证头包括：

- `Authorization`
- `token`
- `access-token`
- `x-token`
- `session-id`
- 自定义业务认证头

不得凭空假设认证头名称，应从代码、配置、拦截器、网关规则或用户说明中确认。

### 8. 完整请求体结构示例

```json
{
  "token": "{项目 token}",
  "title": "接口标题",
  "path": "/user/detail",
  "method": "POST",
  "catid": "{分类 ID}",
  "status": "done",
  "desc": "<h2>功能说明</h2><p>接口功能说明</p><h2>请求头</h2><table>...</table><h2>请求参数</h2><pre><code>{...}</code></pre><h3>请求参数说明</h3><table>...</table><h2>响应示例</h2><pre><code>{...}</code></pre><h3>响应字段说明</h3><table>...</table><h2>响应码</h2><table>...</table><h2>权限要求</h2><p>...</p>",
  "markdown": "## 功能说明\n\n接口功能说明\n\n## 请求头\n\n| Header | 必填 | 示例值 | 说明 |\n|---|---|---|---|\n| Content-Type | 是 | application/json | 请求体类型 |\n| Authorization | 是 |  | 认证信息 |\n\n## 请求参数\n\n```json\n{\n  \"userId\": \"10001\"\n}\n```\n\n### 请求参数说明\n\n| 参数名 | 类型 | 必填 | 示例值 | 说明 |\n|---|---|---|---|---|\n| userId | string | 是 | 10001 | 用户 ID |\n\n## 响应示例\n\n```json\n{\n  \"success\": true,\n  \"code\": 0,\n  \"message\": \"ok\",\n  \"data\": {\n    \"userId\": \"10001\",\n    \"nickname\": \"Tom\"\n  }\n}\n```\n\n### 响应字段说明\n\n| 字段路径 | 类型 | 示例值 | 说明 |\n|---|---|---|---|\n| success | boolean | true | 请求是否成功 |\n| code | number | 0 | 响应码，0 表示成功 |\n| message | string | ok | 响应提示信息 |\n| data | object |  | 响应数据 |\n| data.userId | string | 10001 | 用户 ID |\n| data.nickname | string | Tom | 用户昵称 |\n\n## 响应码\n\n| code | 说明 | 触发场景 |\n|---|---|---|\n| 0 | 成功 | 请求处理成功 |\n| 1001 | 参数校验失败 | 请求参数缺失或格式错误 |\n\n## 权限要求\n\n需要登录后访问。",
  "req_headers": [
    {
      "name": "Content-Type",
      "value": "application/json",
      "required": "1"
    },
    {
      "name": "Authorization",
      "value": "",
      "required": "1"
    }
  ],
  "req_body_type": "json",
  "req_body_is_json_schema": true,
  "req_body_other": "{\"type\":\"object\",\"properties\":{\"userId\":{\"type\":\"string\",\"description\":\"用户 ID\"}},\"required\":[\"userId\"]}",
  "res_body_type": "json",
  "res_body_is_json_schema": true,
  "res_body": "{\"type\":\"object\",\"properties\":{\"success\":{\"type\":\"boolean\",\"description\":\"请求是否成功\"},\"code\":{\"type\":\"number\",\"description\":\"响应码\"},\"message\":{\"type\":\"string\",\"description\":\"响应提示信息\"},\"data\":{\"type\":\"object\",\"description\":\"响应数据\",\"properties\":{\"userId\":{\"type\":\"string\",\"description\":\"用户 ID\"},\"nickname\":{\"type\":\"string\",\"description\":\"用户昵称\"}}}}}"
}
```

### 9. 响应体统一包装规则

应根据项目真实统一响应结构生成响应 Schema。

常见结构示例：

```json
{
  "type": "object",
  "properties": {
    "success": {
      "type": "boolean",
      "description": "请求是否成功"
    },
    "code": {
      "type": "number",
      "description": "响应码"
    },
    "message": {
      "type": "string",
      "description": "响应提示信息"
    },
    "data": {
      "type": "object",
      "description": "响应数据",
      "properties": {}
    }
  }
}
```

如果项目实际使用其他字段名，例如：

- `result`
- `errCode`
- `errMsg`
- `msg`
- `rows`
- `total`

应以项目真实结构为准，不能强行套用示例字段。

## 第三步：获取或创建接口分类

同步前，先获取项目分类列表：

```http
GET {YAPI_HOST}/api/interface/getCatMenu?project_id={project_id}&token={token}
```

如果对应分类不存在，则创建分类：

```http
POST {YAPI_HOST}/api/interface/add_cat
Content-Type: application/x-www-form-urlencoded

token={token}&project_id={project_id}&name={分类名称}&desc={分类描述}
```

分类命名应根据业务域确定。

示例：

| 业务域 | 分类名称 |
|---|---|
| `user` | 用户管理 |
| `order` | 订单管理 |
| `goods` | 商品管理 |
| `payment` | 支付管理 |
| `device` | 设备管理 |
| `message` | 消息管理 |
| `config` | 配置管理 |

如果无法从包路径、类名或接口路径判断分类，应询问用户。

## 第四步：同步接口到 YApi

推荐使用 `save` 接口进行新增或更新：

```http
POST {YAPI_HOST}/api/interface/save
Content-Type: application/json
```

请求体示例：

```json
{
  "token": "...",
  "title": "...",
  "path": "...",
  "method": "POST",
  "catid": 1,
  "status": "done",
  "desc": "...",
  "markdown": "...",
  "req_headers": [],
  "req_body_type": "json",
  "req_body_is_json_schema": true,
  "req_body_other": "...",
  "res_body_type": "json",
  "res_body_is_json_schema": true,
  "res_body": "..."
}
```

`save` 接口会根据 `path` + `method` 自动判断新增或更新。

如果需要强制新增，可使用：

```http
POST {YAPI_HOST}/api/interface/add
```

如果需要强制更新，可使用：

```http
POST {YAPI_HOST}/api/interface/up
```

发送请求体时必须使用 UTF-8 编码，避免中文乱码。

PowerShell 示例：

```powershell
Invoke-RestMethod `
  -Uri $url `
  -Method POST `
  -Body ([System.Text.Encoding]::UTF8.GetBytes($jsonBody)) `
  -ContentType "application/json; charset=utf-8"
```

## 第五步：验证同步结果

无需调用业务接口验证，直接根据 YApi OpenAPI 响应判断。

### 成功

当响应中：

```json
{
  "errcode": 0
}
```

表示同步成功。

输出接口所在分类访问链接：

```text
{YAPI_HOST}/project/{project_id}/interface/api/cat_{catid}
```

### 失败

如果响应中 `errcode` 不为 `0`，输出：

- `errmsg`
- 当前同步的接口路径
- 当前使用的项目 ID
- 当前分类 ID
- 建议排查项

常见失败原因：

- token 无效
- project_id 不匹配
- catid 不存在
- path 格式错误
- JSON Schema 字符串格式错误
- 请求体字段缺失
- YApi 服务异常

## 使用示例

### 示例 1：为单个 Controller 方法生成文档

用户说：

```text
为 UserController 的 detail 方法生成接口文档并同步到 YApi
```

处理步骤：

1. 读取 `UserController.java`
2. 定位 `detail` 方法
3. 提取接口路径、请求方法、标题、描述
4. 解析请求 Param 类
5. 追踪 Service 返回值和 VO 字段
6. 构建请求 JSON Schema
7. 构建响应 JSON Schema
8. 生成 `markdown` 和 `desc`
9. 获取或创建 YApi 分类
10. 调用 `save` 接口同步
11. 返回 YApi 链接

### 示例 2：为某个业务域批量生成文档

用户说：

```text
为 user 目录下所有接口生成文档
```

处理步骤：

1. 扫描 `user` 业务目录
2. 找到所有 Controller / Handler
3. 逐个提取接口信息
4. 统一归类到“用户管理”
5. 批量同步到 YApi
6. 汇总成功和失败结果

### 示例 3：使用指定 token 同步

用户说：

```text
用 token xxx 为 OrderController 生成文档
```

处理步骤：

1. 使用用户提供的 token
2. 调用项目查询接口获取 `project_id` 和 `basepath`
3. 分析 `OrderController`
4. 去掉 path 中的 `basepath` 前缀
5. 同步接口到对应项目

## 注意事项

1. 同步前必须确认 token 对应项目是否正确。
2. 接口路径必须去掉项目 `basepath` 前缀。
3. 如果接口已存在，相同 `path` + `method` 使用 `save` 会自动更新。
4. 请求参数和响应体必须使用 JSON Schema 格式。
5. 必须设置 `req_body_is_json_schema: true`。
6. 必须设置 `res_body_is_json_schema: true`。
7. 备注必须同时设置 `desc` 和 `markdown`。
8. DTO 字段注释是生成文档质量的关键，字段缺少注释时应提醒开发者补充。
9. 嵌套对象字段必须递归解析。
10. List / Set / 数组类型必须解析元素泛型。
11. 响应字段必须从真实代码链路中追踪，不能猜测。
12. 错误码必须追踪到最终数字 code。
13. 权限要求必须根据真实代码、注解或配置填写。
14. 无法确认的信息应明确标注，不得编造。
15. 同步失败时应输出 YApi 返回的 `errmsg`，并给出排查建议。

## YApi OpenAPI 接口速查

| 功能 | Method | Path |
|---|---|---|
| 获取项目信息 | GET | `/api/project/get?token={token}` |
| 获取分类列表 | GET | `/api/interface/getCatMenu?project_id={id}&token={token}` |
| 新增分类 | POST | `/api/interface/add_cat` |
| 新增接口 | POST | `/api/interface/add` |
| 更新接口 | POST | `/api/interface/up` |
| 新增或更新接口 | POST | `/api/interface/save` |
| 获取接口列表 | GET | `/api/interface/list?project_id={id}&token={token}&page=1&limit=1000` |
| 获取接口详情 | GET | `/api/interface/get?id={id}&token={token}` |
```
