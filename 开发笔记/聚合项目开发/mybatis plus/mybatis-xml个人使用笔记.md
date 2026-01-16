## like 查询

```java
SELECT  
*  
FROM  
user  
WHERE  
name like CONCAT('%',#{name},'%')
```

## in

```java
<foreach  item="item" collection="listTag" index="index"  open="(" separator="," close=")">

#{item}

</foreach>
```
## if-else

> 其中choose为一个整体 
when是if 
otherwise是else

```java
<select id="selectSelective" resultMap="xxx" parameterType="xxx">
    select
    <include refid="Base_Column_List"/>
    from xxx
    where del_flag=0
    <choose>
        <when test="xxx !=null and xxx != ''">
            and xxx like concat(concat('%', #{xxx}), '%')
        </when>
        <otherwise>
            and xxx like '**%'
        </otherwise>
    </choose>
</select>
```
## 插入语句并校验是否存在，存在就不执行的例子

> 数据存在就不插入
> DUAL 是个零时表，临时储存数据
> insert 默认不反悔id的，需要加上keyColumn="id" keyProperty="id" useGeneratedKeys="true" 这几个参数

```java
<insert id="saveCommentOnlyData" keyColumn="id" keyProperty="id" useGeneratedKeys="true"
            parameterType="shop.easysell.lib.facebook.domain.po.LiveComment">
        insert into l_live_comment (live_id, face_comment_id, from_customer_id, from_id, from_name, to_id, to_name,
                                    content, is_keyword, is_fans, error_msg_status, created)
        select #{liveComment.liveId}        as live_id,
               #{liveComment.faceCommentId} as face_comment_id,
               #{liveComment.fromCustomerId} as from_customer_id,
               #{liveComment.fromId}         as from_id,
               #{liveComment.fromName}      as from_name,
               #{liveComment.toId}          as to_id,
               #{liveComment.toName}        as to_name,
               #{liveComment.content}       as content,
               #{liveComment.isKeyword}     as is_keyword,
               #{liveComment.isFans}        as is_fans,
               #{liveComment.errorMsgStatus} as error_msg_status,
               #{liveComment.created}       as created
        from DUAL
        where not exists(select id
                         from l_live_comment
                         where live_id = #{liveComment.liveId} and face_comment_id = #{liveComment.faceCommentId})
    </insert>
```
## 编写动态排序

> 由于业务之中，出现动态排序的需求，后端不需要平凡改动排序规则，需要前端判断传参

**排序实体**

```java
package com.ushangxie.cca.common.lib.domain.dto;

import io.swagger.annotations.ApiModelProperty;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class SortFieldDTO {

    @ApiModelProperty("字段")
    private String field;

    @ApiModelProperty("字段")
    private Boolean isAsc;

}

```
**传参对象**

```java
package com.ushangxie.cca.lib.community.domain.req;

import com.ushangxie.cca.common.lib.domain.dto.SortFieldDTO;
import com.ushangxie.cca.common.lib.domain.dto.req.ApiPageReq;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;
import lombok.EqualsAndHashCode;

import java.util.List;

@EqualsAndHashCode(callSuper = true)
@Data
@ApiModel("admin圈子分页-请求实体")
public class AdminPageCircleReq extends ApiPageReq {

    @ApiModelProperty(value = "关键字")
    private String keyword;

    @ApiModelProperty("排序: user_number（成员数量）,post_number（帖子数量）")
    private List<SortFieldDTO> sortFields;

}

```
**xml实体**

> 记住循环的不能使用#{},会报错（超出边界）。得用${}（预防sql注入，加入''）

```java
<select id="adminPage" resultType="com.ushangxie.cca.lib.community.domain.resp.AdminPageCircleResp">
        select c.id as id,
        c.`name` as `name`,
        c.created as created,
        c.create_id as create_id,
        c.create_name as create_name,
        cs.user_number as user_number,
        cs.post_number as post_number
        from cca_circle c
        left join cca_circle_statis cs on c.id = cs.circle_id
        order by
        <choose>
            <when test="adminPageCircleReq.sortFields != null">
                <foreach collection="adminPageCircleReq.sortFields" item="item" separator=",">
                    '${item.field}'
                    <choose>
                        <when test="item.isAsc == true">
                            asc
                        </when>
                        <otherwise>
                            desc
                        </otherwise>
                    </choose>
                </foreach>
            </when>
            <otherwise>
                created desc
            </otherwise>
        </choose>
    </select>
```
**运行效果**

```java
select c.id as id, c.`name` as `name`, c.created as created, c.create_id as create_id, c.create_name as create_name, cs.user_number as user_number, cs.post_number as post_number from cca_circle c left join cca_circle_statis cs on c.id = cs.circle_id order by created desc LIMIT 0,0
```

需要返回 boolean类型


```xml
<select id="isExists" resultType="boolean">  
    SELECT EXISTS(SELECT 1  
                  FROM table WHERE username = #{username})  
</select>
```

## 批量更新update的sql写法


```xml
UPDATE table  
SET  
`activitySort` =  
<foreach collection="list" item="item" index="index" separator=" " open="CASE id" close="END">  
    WHEN #{item.id} THEN #{item.activitySort}  
</foreach>  
WHERE id IN  
<foreach collection="list" item="item" index="index" separator="," open="(" close=")">  
    #{item.id}  
</foreach>
```

> 效果


```
UPDATE table
SET 
    `activitySort` = CASE id
        WHEN 1 THEN 10
        WHEN 2 THEN 20
    END
WHERE id IN (1, 2)
```


## 插入和更新语法

open_flag 是 tinyint(1)
created,updated 是 datetime

在xml使用时，实体类是openFlag boolean的，created 是date，在插入sql的时候参数是：

false,"2026-01-06 19:50:36","2026-01-06 19:50:36"。会插入异常，最好处理一下类型。
CAST(#{po.openFlag} AS UNSIGNED)，CAST(#{po.created} AS DATETIME)

```
INSERT INTO notice_open_config
(account_id, module, notice_type, business_type, open_flag, created, updated)
VALUES
(#{po.accountId}, #{po.module}, #{po.noticeType}, #{po.businessType}, 
 CAST(#{po.openFlag} AS UNSIGNED), 
 CAST(#{po.created} AS DATETIME), 
 CAST(#{po.updated} AS DATETIME))
ON DUPLICATE KEY UPDATE
open_flag = CAST(#{po.openFlag} AS UNSIGNED),
updated = CAST(#{po.updated} AS DATETIME)
```


## 枚举的使用


```
<if test="sortRule != null and sortRule != '' and sortRule == @com.ljq.enums.PostSortRule@liked.name()"></if>
```
