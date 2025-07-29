> 抓包的时候遇到问题，Http协议接口是可以正常抓到的，但https一直显示unknown（未知）

![[Pasted image 20250526121205.png]]
> 发生原因：默认情况下，Charles只能抓取http的包，手机上面访问app接口的时候会出现unknown，是因为返回https接口需要证书信任

### 第一步：确认证书安装

![[Pasted image 20250526121254.png]]

> 配置charles证书：打开charles，Help-->SSL Proxy-->install Charles Root Certificate，

点击安装，这里敲个黑板，存储证书时一定要选择"受信任的根证书版本机构" ，否则仅安装但证书未信任是不够的，仍然会出现unknown的问题，接下来导入证书

**1.1点击安装证书**

![[Pasted image 20250526121355.png]]
**1.2  选择存储位置，看个人情况，存储位置为当前用户或本地计算机均可**

![[Pasted image 20250526121426.png]]

**1.3 选择将所有的证书都放入下列储存，注意一定要选择[受信任的根证书版本机构]，接下来下一步下一步即可按成证书认证。**

![[Pasted image 20250526121500.png]]

### 第二步：确认手机设备证书安装

![[Pasted image 20250526121518.png]]

点击charles，Help-->SSL Proxy--> Install charles root certificate on a Mobile Device or remote browser…，打开浏览器登录网址：chls.pro/ssl 安装证书。

打开手机：设置-->通用-->描述文件与设备管理，安装charles证书即可。

备注：如果浏览器访问地址chls.pro/ssl无法下载时，可打开链接https://www.charlesproxy.com/assets/legacy-ssl/charles.crt下载charles证书

![[Pasted image 20250526121536.png]]

再画个重点，本人问题就出在此安装完并没有信任证书，于是访问http协议接口出现红色unknown，设置路径：**通用-关于本机里面设置信任该证书**。

### 第三步：设置ssl proxy Setting中的ssl proxying的代理网址

如果上述操作已完成还是unknown，接下来要检查下SSL Proxying Settings是否设置允许访问的域名、端口，设置允许所有的443的端口访问，因为https访问的端口号是443，或者直接输入*，允许所有域名、端口访问

> 直接填 *
> 主机和d

![[Pasted image 20250729173735.png]]

![[Pasted image 20250526121606.png]]

### 第四步 手机配置代理访问，接下来就不详细叙述了，相信各位小伙伴都是OK的

![[Pasted image 20250526121637.png]]

完成以上操作，恭喜你就完成Charles抓取HTTP(S)数据包的所有配置了，查看Charles,我们可以看到数据包的内容了。 

注意：如果完成上述操作还是无法链接代理，这时请确认：

1.电脑浏览器代理是否已打开，需进行关闭；

2.另不知道什么原因，原8888端口无法使用，可换下端口号尝试；

3.如果是原来可以链接，突然不行，可删除证书后重新下载安装可纠正操作；

## 手机连接socks5的app

[SocksDroid.apk](https://package.huashengdaili.com/SocksDroid.apk)


## vmos pro安装证书

> 系统必须要用root权限。

花瓶的证书，导出pem的证书文件。

Charles 进入 `Help > SSL Proxying > Save Charles Root Certificate`, 导出 pem 格式证书.

电脑必须装 open ssl软件

[Win32/Win64 OpenSSL Installer for Windows - Shining Light Productions](https://slproweb.com/products/Win32OpenSSL.html "OpenSSL 下载地址")

```powershell
openssl x509 -subject_hash_old -in charles-proxy-ssl-proxying-certificate.pem
```

> charles-proxy-ssl-proxying-certificate.pem 是导出的证书文件

上面输出的结果类似于:

```abnf
faf57fe3
-----BEGIN CERTIFICATE-----
MIIFMDCCBBigAwIBAgIGAXWqowQPMA0GCSqGSIb3DQEBCwUAMIGbMSwwKgYDVQQD
....
aXDrm30UE6+dWdQ3n0ePVLNcHV+ZrIqwka94M/t8HavZpm4y
-----END CERTIFICATE-----
```

然后将 `charles-proxy-ssl-proxying-certificate.pem 文件重命名`为 `faf57fe3.0`(`faf57fe3`需要根据你自己生成 的结果进行调整)

**拷贝文件**, 将生成好的证书文件拷贝到 `/system/etc/security/cacerts/` 目录下, 并且修改文件权限:

手机可以通过  root explorer 软件 授权root权限。将`faf57fe3.0` 放到 cacerts里。