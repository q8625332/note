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
然后我们通过 ssh-keygen -t rsa -C "你的邮箱" 生成SSH Key

![[Pasted image 20240116181450.png]]

生成的 SSH Key可以去到 `C:\Users\用户名\.ssh` 这个目录查看

![[Pasted image 20240116182143.png]]

> 注意这个id_rsa.pub文件，里面是存储了公钥，接下来我们会将里面的内容拿到github上面去添加SSH key

**将ssh key加入github配置中**

![[Pasted image 20240116182056.png]]
