> github


```
git clone https://github.com/FluxionNetwork/fluxion.git
cd fluxion
sudo ./fluxion.sh

```

> Gitee


```
git clone https://gitee.com/youzicha123/fluxion.git

LANG=en_US.UTF-8 sudo ./fluxion/fluxion.sh 

# 查看显示网络接口信息
ip link
# 开启网卡
ip link set wlan0 up
# 关闭网卡
ip link set wlan0 down
# 开启网卡的混合模式
ip link set wlan0 promisc on
# 关闭网卡的混合模式
ip link set wlan0 promisc off
# 设置网卡队列长度
ip link set wlan0 txqueuelen 1200
# 设置网卡最大传输单元
ip link set wlan0 mtu 1400


```
