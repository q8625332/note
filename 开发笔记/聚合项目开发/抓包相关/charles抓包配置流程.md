> 抓包的时候遇到问题，Http协议接口是可以正常抓到的，但https一直显示unknown（未知）

![[Pasted image 20250526121205.png]]
> 发生原因：默认情况下，Charles只能抓取http的包，手机上面访问app接口的时候会出现unknown，是因为返回https接口需要证书信任

### 第一步：确认证书安装
![[Pasted image 20250526121254.png]]

> 配置charles证书：打开charles，Help-->SSL Proxy-->install Charles Root Certificate，

点击安装，这里敲个黑板，存储证书时一定要选择"受信任的根证书版本机构" ，否则仅安装但证书未信任是不够的，仍然会出现unknown的问题，接下来导入证书

**1.1点击安装证书**
![[Pasted image 20250526121355.png]]
