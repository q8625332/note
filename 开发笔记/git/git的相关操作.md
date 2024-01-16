## 解决git请求github时候超时的问题

> 错误如下

```
Failed to connect to github.com port 443 after 21107 ms: Timed out
```
**当前Git环境所有配置**

```
git config --global --list
```
![[Pasted image 20240116181254.png]]

**要确保你的用户名和邮箱对应的上你的github，如果对应不上的话需要通过以下命令重新配置以下就可以了**

```
git config --global user.name "Rodma1" 
git config --global user.email "你的邮箱"
```
然后我们通过 ```
``` 生成SSH Key