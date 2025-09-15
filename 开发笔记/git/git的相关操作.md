## 解决git请求github时候超时的问题

> 错误如下

```
Failed to connect to github.com port 443 after 21107 ms: Timed out
```
**当前Git环境所有配置**

```
git config --global --list
```
[![6f53764081eb6376481aa69cf05b50966070.png](https://img.meituan.net/portalweb/6f53764081eb6376481aa69cf05b50966070.png)](https://img.meituan.net/portalweb/6f53764081eb6376481aa69cf05b50966070.png)

**要确保你的用户名和邮箱对应的上你的github，如果对应不上的话需要通过以下命令重新配置以下就可以了**

```
git config --global user.name "Rodma1" 
git config --global user.email "你的邮箱"
```
然后我们通过 ssh-keygen -t rsa -C "你的邮箱" 生成SSH Key

[![be90d0900fc220bd39f58092b0d7e06020753.png](https://img.meituan.net/portalweb/be90d0900fc220bd39f58092b0d7e06020753.png)](https://img.meituan.net/portalweb/be90d0900fc220bd39f58092b0d7e06020753.png)

生成的 SSH Key可以去到 `C:\Users\用户名\.ssh` 这个目录查看

[![e18b7a7dd0680f91a455a63df55b629240067.png](https://img.meituan.net/portalweb/e18b7a7dd0680f91a455a63df55b629240067.png)](https://img.meituan.net/portalweb/e18b7a7dd0680f91a455a63df55b629240067.png)

> 注意这个id_rsa.pub文件，里面是存储了公钥，接下来我们会将里面的内容拿到github上面去添加SSH key

**将ssh key加入github配置中**

[![dc3255d97346662d3b7fa631b2acf4ed52172.png](https://img.meituan.net/portalweb/dc3255d97346662d3b7fa631b2acf4ed52172.png)](https://img.meituan.net/portalweb/dc3255d97346662d3b7fa631b2acf4ed52172.png)

**本地添加github的地址解析**

```
140.82.112.3 github.com
```
[![27170c35eb9b5594fd277ce82793ccd932047.png](https://img.meituan.net/portalweb/27170c35eb9b5594fd277ce82793ccd932047.png)](https://img.meituan.net/portalweb/27170c35eb9b5594fd277ce82793ccd932047.png)

测试是否连通 `ssh -T git@github.com`

[![8a47f3b3f6756bb56a0ac1bb5ed4bf135202.png](https://img.meituan.net/portalweb/8a47f3b3f6756bb56a0ac1bb5ed4bf135202.png)](https://img.meituan.net/portalweb/8a47f3b3f6756bb56a0ac1bb5ed4bf135202.png)
