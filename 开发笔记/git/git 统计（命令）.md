## 查询某人某个时刻提交了多少代码

> added 添加代码
> removed 删除代码
> total 总代码

```java
git log --author=刘俊秦 --since='2023-08-01 00:00:00' --until='2023-08-23 23:00:00' --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s,total lines: %s\n", add, subs, loc }'
```
**效果图**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bd0eabc6e0284ce780041a259287ed22.png)
## 简洁版（查询某人某个时刻提交了多少代码）

```java
git log --author=刘俊秦 --since='2023-08-01 00:00:00' --until='2023-08-23 23:00:00' --pretty=tformat: --numstat | awk '{loc+=($1-$2)} END {print loc}'
```
**效果图**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/584589e2a82b4d0db44bfb84f11825e6.png)
