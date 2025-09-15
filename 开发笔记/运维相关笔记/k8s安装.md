## 简介

> 单机docker方便使用。但多台机子管理起来就不方便。
> 本人搭建了两次（为什么会搭建两次，嘻嘻。因为是刚学的，不太熟），搭建出来了。（不要在意，图片ip地址不一致问题）
> 分享于我的粉丝们

## 对你们有帮助，请留下你们的评论

## k8s的整体架构
![在这里插入图片描述](https://img-blog.csdnimg.cn/0eddeed4b0fb48428b8a7be0c9d920ba.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_16,color_FFFFFF,t_70,g_se,x_16)
## 命令集

```java
#重置k8s
kubeadm reset
#重新获取加入token
kubeadm token create --print-join-command
#获取Dashboard登录token（这个更具个人配置的用户和权限范围去查，仅供参考）
kubectl describe secret -n kubernetes-dashboard $(kubectl get secret -n kubernetes-dashboard | grep dashboard-admin | awk '{print $1}')
```

## 预先准备三台服务器（或虚拟机）

> k8s-master
> k8s-node1
> k8s-node2

**Kubernetes部署环境要求**

（1）一台或多台机器，操作系统CentOS 7.x-86_x64

（2）硬件配置：内存2GB或2G+，CPU 2核或CPU 2核+；

（3）集群内各个机器之间能相互通信；

（4）集群内各个机器可以访问外网，需要拉取镜像；

（5）禁止swap分区；

效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a9a2f2d7cf324583b63f9d6f839c48a0.png)
如何配置我就不详细描述了，不懂的百度

## Kubernetes部署环境准备

[![46a066ffbb3161719d8f64a74d6d314c33086.png](https://img.meituan.net/portalweb/46a066ffbb3161719d8f64a74d6d314c33086.png)](https://img.meituan.net/portalweb/46a066ffbb3161719d8f64a74d6d314c33086.png)

输入如下命令来永久开启端口

```linux
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10259/tcp
firewall-cmd --permanent --add-port=10257/tcp
```

```java
# 关闭防火墙

systemctl stop firewalld

systemctl disable firewalld

# 关闭selinux

sed -i 's/enforcing/disabled/' /etc/selinux/config  #永久

setenforce 0  #临时

# 关闭swap（k8s禁止虚拟内存以提高性能）

sed -ri 's/.*swap.*/#&/' /etc/fstab #永久

swapoff -a #临时

# 在master添加hosts

cat >> /etc/hosts << EOF

192.168.95.247 k8smaster

192.168.95.248 k8snode1

192.168.95.249 k8snode2

EOF

# 设置网桥参数

cat > /etc/sysctl.d/k8s.conf << EOF

net.bridge.bridge-nf-call-ip6tables = 1

net.bridge.bridge-nf-call-iptables = 1

EOF

sysctl --system  #生效

# 时间同步

yum install ntpdate -y

ntpdate time.windows.com
```

**配置加载内核及IPv4 数据包转发**

```linux
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
 
# 重新加载 sysctl 配置以使更改立即生效
sysctl --system
```

## 那么接下来需要搭建：kubeadm、kubelet、kubectl

> 先设置源头

```java
cat > /etc/yum.repos.d/kubernetes.repo << EOF

[kubernetes]

name=Kubernetes

baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64

enabled=1

gpgcheck=0

repo_gpgcheck=0

gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

EOF
```
**安装**

```java
yum install kubelet-1.22.2 kubeadm-1.22.2 kubectl-1.22.2 -y
```

**然后执行**：

```java
systemctl enable kubelet.service
```
不然会提示警告；

```java
yum list installed | grep kubelet

yum list installed | grep kubeadm

yum list installed | grep kubectl
```

**查看安装的版本： `kubelet --version`**

Kubelet：运行在cluster所有节点上，负责启动POD和容器；

Kubeadm：用于初始化cluster的一个工具；

Kubectl：kubectl是kubenetes命令行工具，通过kubectl可以部署和管理应用，查看各种资源，创建，删除和更新组件；

以上每台机子都要运行一遍


**部署Kubernetes Master主节点**

```java
kubeadm init --apiserver-advertise-address=192.168.95.247 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.22.2 --service-cidr=10.96.0.0/12 --pod-network-cidr=10.244.0.0/16
```

此命令在master机器上执行
![在这里插入图片描述](https://img-blog.csdnimg.cn/0108486748c94804b0801a743f451376.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/7e98589f202d41e7a1f324c50b6c2c7b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2419c34b09cc46f2966c827175b97769.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)

```java
mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**安装网络插件（只需要master安装就行了）：**

拉取：

```java
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

没有安装wget的话，安装一下：

```java
yum install wget
```

> 拉取不了的话，有以下解决办法

先设置一下 hosts

```java
cat >> /etc/hosts << EOF
199.232.68.133 raw.githubusercontent.com
EOF
```
重新执行拉取操作。
下载完之后，创建并查看：

```java
#创建
kubectl  create -f kube-flannel.yml
#查看
kubectl  get pod -n kube-system
```

在244和245机子下分别运行：

> 运行243初始话的加入命令

```java
kubeadm join 192.168.95.247:6443 --token wsfwk9.cfncltj3zmng7in2 \
	--discovery-token-ca-cert-hash sha256:e63baea55e3d3a22ba1e71327e8addcb90adb422db2459ac57f354a426cd05da
```
效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/28ca6e6146164453b70efd83b8d8ab01.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
查询一下：
```java
kubectl get nodes
```
效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/dd54edd83e9a40c98224d9624aaa01c5.png)

> NotReady 表示没准备好，需要等一下
> Ready 就表示准备好了

![在这里插入图片描述](https://img-blog.csdnimg.cn/9ad35d557da444398178c66f178cb0c9.png)
## 安装web-ui

> 方便用于管理

[选择符合的web-ui版本](https://github.com/kubernetes/dashboard/releases)

**安装**

> 安装dashboard:v2.3.1 为例子

```java
#拉取下来
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml
```
**修改recommended.yaml**

```java
vi recommended.yaml

#修改如下
spec:
  #添加type NodePort
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      #添加nodePort 对外端口
      nodePort: 31001
  selector:
    k8s-app: kubernetes-dashboard
#因为默认情况下，service的类型是cluster IP，需更改为NodePort的方式，便于访问，也可映射到指定的端口
```

查看运行情况：

```java
[root@k8smaster ~]# kubectl get pod -n kubernetes-dashboard
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-856586f554-f5fkb   1/1     Running   0          3m46s
kubernetes-dashboard-67484c44f6-824zh        1/1     Running   0          3m46s
```
查看svc

```java
[root@k8smaster ~]# kubectl get svc -n kubernetes-dashboard
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP   10.110.54.135    <none>        8000/TCP        163m
kubernetes-dashboard        NodePort    10.111.126.118   <none>        443:30230/TCP   3m58s
```

**客户端访问测试**

> 注意一定要用https 访问。否则访问不了，踩了一个小坑

![在这里插入图片描述](https://img-blog.csdnimg.cn/c0f13109f2724b7ca192ec3cdf0c6e37.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
**用token登录**
![在这里插入图片描述](https://img-blog.csdnimg.cn/a2f26d7bb20e486d86e0e35e4b441152.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
**yml已经创建了用户**

> Kubernetes Dashboard 默认部署时，只配置了最低权限的 RBAC
> 所以我们要创建一个可以管理集群的

**创建 dashboard-sa.yaml**

```java
vi dashboard-sa.yaml

#添加以下内容
apiVersion: v1
kind: ServiceAccount
metadata:
  name: dashboard-admin
  namespace: kubernetes-dashboard

#保存后运行
kubectl apply -f dashboard-sa.yaml
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/0c24797d8bad4437b59a7d36fb856029.png)


**创建 dashboard-clusterrolebinding.yaml**

```java
vi dashboard-clusterrolebinding.yaml

#添加以下内容
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: dashboard-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: dashboard-admin
  namespace: kubernetes-dashboard


#保存后运行
kubectl apply -f dashboard-clusterrolebinding.yaml
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/dcac17374d174fb7a52b1f26918fbc14.png)
查询 token

```java
[root@k8smaster ~]# kubectl describe secret -n kubernetes-dashboard $(kubectl get secret -n kubernetes-dashboard | grep dashboard-admin | awk '{print $1}')
Name:         dashboard-admin-token-rllwn
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: dashboard-admin
              kubernetes.io/service-account.uid: e36e516d-d770-458f-a134-2a8ed744311c

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1099 bytes
namespace:  20 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IkgtZWJ6c3RNTGF5Mi00RnZZTWpYdnNYd05GOGRjTG1KOVdOMU9wTm1pUkEifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4tdG9rZW4tcmxsd24iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGFzaGJvYXJkLWFkbWluIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiZTM2ZTUxNmQtZDc3MC00NThmLWExMzQtMmE4ZWQ3NDQzMTFjIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmVybmV0ZXMtZGFzaGJvYXJkOmRhc2hib2FyZC1hZG1pbiJ9.UwET9K7Q80lK5RzCsW854GOF-tqZUj3ZI7PLDCcd0W7JlzZTkl3kLAOqwvY3tby028i2vSVx3COakcpE3wrtrywKPI3IO-y5MSti5gPc_4D-QKSrPRb94ggn71jiWl7oGuLo5GiRJr0wnW1WPeMyNZz34XXhZIC8Kh-ioe5DomEnGL0aC12VDoXCs6YDUT3xN7J8uJHPVlyNuVPXy9sbnHDkycTfyIxjAPZOkhmYDbuv2hPWf1i-0v35jG3TUlVJKh_7KN5xIhoJNoxIdhcHVhHExnbBrY6Yr3Y1veOL9qWtWFh4Juoi0gKfOJLEd56SoJEqNS5WCU-icLORF8M4Vw

```

将token复制后登录
![在这里插入图片描述](https://img-blog.csdnimg.cn/5072ff9dd34e4c31ac519958c3cadb44.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/353553d59561422eacfdc99c2b9570ac.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)


安装ui 到这里就结束了

卸载 ui

```java
sudo kubectl delete deployment kubernetes-dashboard --namespace=kubernetes-dashboard 
sudo kubectl delete deployment dashboard-metrics-scraper --namespace=kubernetes-dashboard
sudo kubectl delete service kubernetes-dashboard  --namespace=kubernetes-dashboard
sudo kubectl delete role kubernetes-dashboard-minimal --namespace=kubernetes-dashboard
sudo kubectl delete rolebinding kubernetes-dashboard-minimal --namespace=kubernetes-dashboard
sudo kubectl delete sa kubernetes-dashboard --namespace=kubernetes-dashboard
sudo kubectl delete secret kubernetes-dashboard-certs --namespace=kubernetes-dashboard
sudo kubectl delete secret kubernetes-dashboard-csrf --namespace=kubernetes-dashboard
sudo kubectl delete secret kubernetes-dashboard-key-holder --namespace=kubernetes-dashboard
```
## 安装ingress

> 为什么要安装这个呢，我需要对外暴露接口使用nginx会很麻烦，因为pod的ip会产生变化，同步更改nginx很麻烦，所以使用ingress

[github地址](https://github.com/kubernetes/ingress-nginx)

**选择需要的版本**
![在这里插入图片描述](https://img-blog.csdnimg.cn/5f1b43e9c99a47628aa1a987d1aabaa7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
在这里我们安装1.22.2对应的版本 1.0.0
![在这里插入图片描述](https://img-blog.csdnimg.cn/a9e075eab6b34c5793627e06614c99ce.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)

安装
**拉取文件**

```java
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/cloud/deploy.yaml
```

**修改deploy.yaml**

更改里面可以下载到的镜像

```java
#将ingress-nginx的image换成
willdockerhub/ingress-nginx-controller:v1.0.0
#将kube-webhook-certgen的image换成
jettech/kube-webhook-certgen:v1.5.2
```
**先下载镜像**

```java
grep image deploy.yaml
```
**运行**

```java
[root@k8smaster ~]# kubectl apply -f 
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created

```
查看运行状况


![在这里插入图片描述](https://img-blog.csdnimg.cn/bb957c06731c4d1da1ff672e90e5ad8e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/e2fff653b9cb44d0bb39a971a4adc476.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/1f630c9d3e254bc498b3fe67fba27721.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/56405808804946cbb92fea979188d643.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
## 为什么ingress会pending呢

> 因为我们用的是负载均衡不是用nodeport
> 直接用nodeport就不会pending
> 但是用多了nodeport管理ip和端口显然会比较麻烦
> 下面就有解决办法，不要做k8s生态的二等公民

## 安装Metallb 

> 为什么ingress-nginx 是pending模式呢，由于我们使用的是负载均衡模式，所以service会被无限期挂起。
> 如何解决这种情况呢，在公有云会有支持lb的，但是在私有云的裸金属集群是没有lb的。
> 所以Metallb 就是帮我们解决这个问题的
[github的地址](https://github.com/metallb/metallb/tree/metallb-chart-0.10.2)

[官网地址](https://metallb.universe.tf/installation/)

安装

> 如果您在IPVS模式下使用kube代理，那么由于Kubernetes v1.14.2版，您必须启用严格的ARP模式。
>请注意，如果使用kube路由器作为服务代理，则不需要此选项，因为默认情况下它启用严格的ARP。
>您可以通过在当前集群中编辑kube代理配置来实现这一点：

```java
kubectl edit configmap -n kube-system kube-proxy
```
并设置:

```java
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
ipvs:
  strictARP: true
```
担心配置没有更新到，就重启一下master服务器
```java
reboot
```

创建和部署
```java
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml
```
创建metallb的ip分配配置

> 分配分对外地址，与集群一致
> 我这里用的是本地地址，生产环境要用外部可以访问的地址

```java
kind: ConfigMap
apiVersion: v1
metadata:
  name: config
  namespace: metallb-system
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.95.247-192.168.95.250
```

对外地址的分配效果

> ingress终于不会pending了

![在这里插入图片描述](https://img-blog.csdnimg.cn/4306fc111c2249f9865e715ee634ec32.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
## ingress如何代理服务

> 编写yml
>参考以下代码 

```java

apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.ingredemo.com
    http:
      paths:
      - path: /
        backend:
          serviceName: web
```
