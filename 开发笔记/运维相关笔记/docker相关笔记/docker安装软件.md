**强烈**-**建议使用**-**阿里云的docker加速镜像**。不然拉取镜像的速度太慢了*

> win电脑安装docker出现pull不下来的问题
> 报错：`no matching manifest for windows/amd64 10.0.18362 in the manifest list entries`

解决办法：

```java
windows 10 x64安装版本需要开启Hyper-V
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210318223024178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

```java
将 experimental 设置为 true 即可
```

## 安装mysql

```java
#拉取镜像
docker pull mysql:tag

docker run --name 自定义名称 -e MYSQL_ROOT_PASSWORD=自定义密码 -p 3306:3306 -d mysql （可以额外加上） :tag
```

## 安装 mariadb

> mariadb 是mysql 的一个分支，是由开源社区维护的。
> 预防mysql

```java
//拉取
docker pull mariadb

//启动
docker run --name mariadb -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -d mariadb （可以额外加上） :tag
```

## 安装 gitLab 代码版本库
**选择性安装 可以安装（英文版）（中文版）**
```
正式安装（英文版）：
1、拉取镜像
docker pull gitlab/gitlab-ce
```
**确保安装顺利，linux先关闭selinux服务，否则容器内部可能权限不足**

> 本人用的是centos

```
vi /etc/selinux/config
-------------------------------
SELINUX=enforcing  #注释掉
SELINUXTYPE=targeted #注释掉
SELINUX=disabled #增加
:wq! #保存退出
-------------------------------
setenforce 0 #使配置立即生效
```
**创建docker中的网络**

```
docker network create gitlab_net
```
**使用镜像创建容器，并且使重要数据外部挂载到宿主机**

> docker 命令的详解就不做解释了，请参照我的另一篇docker文章

```
（英文配置）
 docker run --name='gitlab' -d \
--net=gitlab_net \
--publish 1443:443 --publish 18080:80 \
--restart always \
--volume /root/docker/gitlab/config:/etc/gitlab \
--volume /root/docker/gitlab/logs:/var/log/gitlab \
--volume /root/docker/gitlab/data:/var/opt/gitlab \
--privileged=true \
gitlab/gitlab-ce:latest
```
**安装中文版**

```
正式安装（中文版）：
1、拉取镜像
docker pull twang2218/gitlab-ce-zh:latest
```
创建需要用的文件夹

```
mkdir /docker/gitlab/config
mkdir /docker/gitlab/logs
mkdir /docker/gitlab/data
```
创建运行容器

```
docker run --detach --publish 9002:443 --publish 9003:80 --publish 9004:22 --name gitlab --restart always --volume /docker/gitlab/config:/etc/gitlab --volume /docker/gitlab/logs:/var/log/gitlab --volume /docker/gitlab/data:/var/opt/gitlab twang2218/gitlab-ce-zh:latest
```
**生成容器后访问**
10.19.210.249:80 即可访问gitlab，访问成功后，会让你输入一个8位的密码，用户名默认：root，这个账户是管理员账户。
到此，用户就可以正常使用了。
```
## 查看容器是否运行起来
docker ps | grep gitlab
```
本人已经装过英文版和中文版
例子（中文版）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200430150243255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
## 安装 nacos

```
#拉取镜像
docker pull nacos/nacos-server
#启动镜像
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server
```
## 安装 redis


## 安装go-fastdfs教程
**拉取镜像到本地**

```java
docker pull sjqzhang/go-fastdfs
```
**创建保存目录**

```java
mkdir -p data/fastdfs_data
```
**命令创建go-fastdfs容器**
```java
docker run --name go-fastdfs --privileged=true -t -p 7999:8080 -v /data/fastdfs_data:/data -e GO_FASTDFS_DIR=/data sjqzhang/go-fastdfs
```
**访问地址：http://127.0.0.1:7999/ 测试是否创建完毕。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191220182321658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE1NjEzMzU=,size_16,color_FFFFFF,t_70)

## 安装go-fastdfs-web
**拉取镜像到本地**

```java
docker pull perfree/fastdfsweb
```

**命令创建go-fastdfs-web容器**

```java
docker run --name fastdfsweb -d -p 8000:8088 perfree/fastdfsweb
```
**记得释放 go-fastdfs的白名单**

> 修改 conf/cfg.json配置文件
> 我是用docker部署的所以 白名单docker地址 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210401203143636.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210401202956608.png)


**配置web的配置**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210401203208294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
配置完成后，登录
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210401203221289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210401203248895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)


## 安装 xxl-job 实现分布式定时任务
下载镜像（需要指定版本号，不然拉取不了。可以直接去 [hub 仓库](https://hub.docker.com/) 查一下）

```java
// Docker地址：https://hub.docker.com/r/xuxueli/xxl-job-admin/     (建议指定版本号)
docker pull xuxueli/xxl-job-admin
```

> 最好自己配置一下数据库，不认xxl-job 连接不到数据库。
> [xxl-job 数据库表 数据库命名 xxl-job](https://gitee.com/xuxueli0323/xxl-job/tree/master/doc/db)

创建容器并运行

```java
docker run -p 8080:8080 -v /tmp:/data/applogs --name xxl-job-admin  -d xuxueli/xxl-job-admin:{指定版本}
/**
* 如需自定义 mysql 等配置，可通过 "-e PARAMS" 指定，参数格式 PARAMS="--key=value  --key2=value2" ；
* 配置项参考文件：/xxl-job/xxl-job-admin/src/main/resources/application.properties
* 如需自定义 JVM内存参数 等配置，可通过 "-e JAVA_OPTS" 指定，参数格式 JAVA_OPTS="-Xmx512m" ；
*/
docker run -e PARAMS="--spring.datasource.url=jdbc:mysql://127.0.0.1:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=" -p 9998:8080 -v /tmp:/data/applogs --name xxl-job-admin  -d xuxueli/xxl-job-admin:{指定版本}
    
```

## 安装Elasticsearch7.x

> jdk需要1.8以上，以下的就重装jdk吧
> 当前用的是2020.6.7号时期的最新版7.7.1

**拉取镜像**
```java
docker pull elasticsearch:7.7.1
```
**启动容器**

> 必须指定java使用的启动大小，不然服务器卡机，就别怪我没有提醒你
> -e "discovery.type=single-node" 这个需要填写，不然7.7.1启动不了。本人已经试过了

```java
docker run -d -e ES_JAVA_POTS="-Xms256m -Xmx256m"  -e "discovery.type=single-node" -p 9200:9200 -p 9300:9300 --name es elasticsearch:7.7.1
```

**访问**

> http://ip:9200
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200617113847621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
## 在docker里的es如何安装 ik分词器呢
> 如果容器不能联网的情况下如何安装
> 版本需要和es版本一张 如果不一致，有可能报错。作者是没试过。你们可以试。哈哈

**先找一个文件夹 下载 ik分词器**

> 没有按照wget 的先安装

```java
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.7.1/elasticsearch-analysis-ik-7.7.1.zip
```
**下载好，cp 进es容器中**

 1.先获取容器的id  （elasticsearch 是容器名）
	
```java
docker inspect -f '{{.ID}}' elasticsearch
```

2.进入容器（-u root 表示以root身份进入）

```java
docker exec -it -u root 469 bash
```
3.复制进入容器

```java
docker cp /home/elasticsearch-analysis-ik-7.7.1.zip 46976a7e3af77f641c3732d44291be293dc4f618f397363773dd863f90552bc5:/usr/share/elasticsearch/plugins
```
4.在plugins文件夹下 创建 ik 文件夹 把压缩包移动进去 unzip 解压 zip 压缩包。在rm -f 删掉压缩包
5.重启 es容器。es容器会自动加载ik插件

**成功效果图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200617173909938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## 安装kibana 管理 es

**拉取镜像**
```java
docker pull kibana:7.7.1
```
**启动容器**

```java
docker run --name kibana -d -p 5601:5601 --link es -e "ELASTICSEARCH_URL=http://192.168.16.4:9200" kibana:7.7.1
```

> 如果连接不到es 需要手动修改 kibana的配置文件
> 本人就是用上面的运行，发现连接不要。查看了容器的配置 发现没有改掉ip地址
> 所以修改掉 重启容器就可以了

```java
docker exec -it 95e0d72eb367（kibana容器id） /bin/bash

进入后：
cd config
vi kibana.yml

修改 http://elasticsearch:9200 为 http://本地ip或者远程ip:9200
//在kibana.yml配置文件中添加一行配置 设置成中文
i18n.locale: "zh-CN"
```
http://ip:5601
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200617145450258.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## 安装logstash

> 下载Logstash镜像

```java
docker pull logstash:7.7.1

#查看镜像
docker images
```
**编辑logstash.yml配置文件**

> logstash.yml配置文件放在宿主机/data/elk/logstash目录下，内容如下：

```java
path.config: /usr/share/logstash/conf.d/*.conf
path.logs: /var/log/logstash
```
**编辑test.conf文件**

> test.conf文件放在宿主机/data/elk/logstash/conf.d目录下，内容如下：

```java
input {
    beats {
        port => 5044
        codec => "json"
    }
}

output {
  elasticsearch { hosts => ["192.168.12.183:9200"] }
  stdout { codec => rubydebug }
}
```

> 启动logstash

```java
docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2 -p 5044:5044 --name logstash -v /data/elk/logstash/logstash.yml:/usr/share/logstash/config/logstash.yml -v /data/elk/logstash/conf.d/:/usr/share/logstash/conf.d/ logstash:7.7.1
```

> 查看容器运行状态

```java
docker ps
docker logs -f logstash
```

## 安装 seata （阿里分布式事务）
**拉取镜像**

```java
docker pull seataio/seata-server
```
**运行容器**

```java
docker run --name seata-server -p 8091:8091 seataio/seata-server
```

> 进入容器，修改 registry.conf 和 file.conf

**修改 registry.conf **

> 修改为nacos 配置
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200724001320946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)


**file.conf**

> 修改储存方式，file改为db，配置数据库

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200716021701397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200716021810508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
**具体配置**
[springboot 集成 seata](https://blog.csdn.net/u011767319/article/details/107374369)

## 安装kafka

> 安装kafka首先就得先安装服务注册中心，kafka是使用zookeeper作为注册中心（作者本人想换成nacos，但是各方考究，查找不倒资料放弃了）

 - 安装zookeeper
 

```java
//下载zookeeper镜像
docker pull wurstmeister/zookeeper
//启动镜像生成容器
docker run -d  --name zookeeper -p 2181:2181 wurstmeister/zookeeper
//线上的可以考虑
docker run -d --restart=always --name zookeeper -p 2181:2181 wurstmeister/zookeeper
```

 - 安装kafka
 

```java
//下载kafka镜像
docker pull wurstmeister/kafka
//启动kafka镜像生成容器
docker run -d --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=192.168.37.1:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.37.1:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka
//线上的可以考虑
docker run -d --restart=always --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=192.168.37.1:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.37.1:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 wurstmeister/kafka
```

 - 验证kafka是否可以使用
 

```java
//进入容器
docker exec -it kafka bash
//进入 /opt/kafka_2.12-2.3.0/bin/ 目录下
cd /opt/kafka_2.13-2.6.0/bin/
//运行kafka生产者发送消息
./kafka-console-producer.sh --broker-list localhost:9092 --topic test
//发送消息

> hello word!
//运行kafka消费者接收消息
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```
## 安装showdoc 在线文档（也可以当笔记使用）
> 安装前请确保你的环境已经装好了docker 。

```java
# 原版官方镜像安装命令(中国大陆用户不建议直接使用原版镜像，可以用后面的加速镜像)
docker pull star7th/showdoc 
# 中国大陆镜像安装命令（安装后记得执行docker tag命令以进行重命名）
docker pull registry.cn-shenzhen.aliyuncs.com/star7th/showdoc
docker tag registry.cn-shenzhen.aliyuncs.com/star7th/showdoc:latest star7th/showdoc:latest 
##后续命令无论使用官方镜像还是加速镜像都需要执行
#新建存放showdoc数据的目录
mkdir -p /showdoc_data/html
chmod  -R 777 /showdoc_data
#启动showdoc容器
docker run -d --name showdoc --user=root --privileged=true -p 4999:80 \
-v /showdoc_data/html:/var/www/html/ star7th/showdoc
```
[官方安装文档地址](https://www.showdoc.com.cn/help?page_id=65610)

**效果图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201117090832164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70#pic_center)
## 安装anaconda3（Python全家桶环境）

 1. 搜索镜像：`docker search anaconda`![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119114049866.png#pic_center)
 2. pull镜像：pull一个最高的`docker pull continuumio/anaconda3`![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119114138790.png#pic_center)
 3. 启动并进入 运行容器。`sudo docker run --name anaconda3 -i -t -p 4001:8888 continuumio/anaconda3 /bin/bash`
 4. 安装xgboost包`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple xgboost`
 5. 运行jupyter notebook，在容器中启动 `jupyter notebook --port 8888 --ip 0.0.0.0 --allow-root` ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119124039789.png#pic_center)

 6. 把前面的127.0.0.1:8888（容器的ip和端口）改为宿主机的ip和8888映射到宿主机的端口12345
 7. `http://10.199.138.5:12345/?token=a6b3189e8f96802b6d193475f0e30908c3a2e16816e1a444`![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119124025643.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70#pic_center)

> 要是想把jupyter后台运行,必须先进入anaconda3容器

```java
nohup jupyter notebook --port 8888 --ip 0.0.0.0 --allow-root >notebook.log 2>&1 &
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119155622919.png#pic_center)

> vim notebook.log 查看下是否启动。启动了会有token。就代表成功了。浏览器访问带上token
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119163007567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70#pic_center)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119162745576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70#pic_center)

## 安装Sentinel

 - 拉取镜像：

```java
docker pull bladex/sentinel-dashboard
```

 - 运行镜像

```java
docker run --name sentinel -d -p 8858:8858 -d bladex/sentinel-dashboard
```

 - 访问dashboard 地址：http://localhost:8858
 - 账号密码都为：sentinel
 - 
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112713105067.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
## 安装Rocketmq
**拉取镜像**

```java
docker pull rocketmqinc/rocketmq
```
**启动rocket**

> 分两部启动：
> 1.启动namesrv
> 2.启动broker服务


**启动namesrv**

```java
//不加日志启动
docker run -d -p 9876:9876 --name rmqnamesrv -e "MAX_POSSIBLE_HEAP=100000000" rocketmqinc/rocketmq sh mqnamesrv
//加日志启动
docker run -d -p 9876:9876 -v /opt/rocketmq/data/namesrv/logs:/root/logs -v /opt/rocketmq/data/namesrv/store:/root/store --name rmqnamesrv -e "MAX_POSSIBLE_HEAP=100000000" rocketmqinc/rocketmq sh mqnamesrv
```
**启动broker服务**

```java
//不加日志启动
docker run -d -p 10911:10911 -p 10909:10909 --name rmqbroker --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" rocketmqinc/rocketmq sh mqbroker
//加日志启动
docker run -d -p 10911:10911 -p 10909:10909 -v /opt/rocketmq/data/broker/logs:/root/logs -v /opt/rocketmq/data/broker/store:/root/store --name rmqbroker --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" rocketmqinc/rocketmq sh mqbroker
```
**安装Rocketmq控制台**

```java
#拉取console镜像
docker pull styletang/rocketmq-console-ng　
#运行console镜像，addr设置成自己虚拟机的IP地址
docker run --name rocketmq-console -e "JAVA_OPTS=-Drocketmq.namesrv.addr=192.168.65.1:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 9875:8080 -d -t styletang/rocketmq-console-ng
```

> 访问控制台：http://虚拟机IP:8080
> 控制台界面如下：集群界面中至少有一条broker说明整体安装成功，否则失败！

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210112214006167.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210112214017195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
## 安装minIo (对象储存)

> 拉取项目

```java
docker pull minio/minio
```

> 运行

```java
//简洁启动
docker run -p 9000:9000 --name minio -d -it minio/minio server /data

docker run -p 9000:9000 --name minio \
-d --restart=always \
-e "MINIO_ACCESS_KEY=admin" \
-e "MINIO_SECRET_KEY=admin123456" \
-v /home/data:/data \
-v /home/config:/root/.minio \
minio/minio server /data
```

> 默认密码为minioadmin

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210507154522825.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210507154546658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

> 图片访问
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210508110609511.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210508110653688.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## 安装minIo(集群)

> 准备3个节点，每个节点创建2个挂载点，由于集群模式下不能使用根磁盘，这里使用docker卷作为挂载点。
3节点的集群，故障一个节点时不影响对集群的读写操作。


1、所有节点配置主机名解析：

```java
cat >> /etc/hosts << EOF
192.168.92.10 minio-1
192.168.92.11 minio-2
192.168.92.12 minio-3
EOF
```

2、配置时间同步，关闭防火墙和selinux。

3、所有节点安装docker(安装就不描述了)

4、部署minio集群，3个节点每个节点挂载2个目录

minio-1节点执行

```java
docker run -d --name minio \
  --restart=always --net=host \
  -e MINIO_ACCESS_KEY=minio \
  -e MINIO_SECRET_KEY=minio123 \
  -v minio-data1:/data1 \
  -v minio-data2:/data2 \
  minio/minio server \
  --address 192.168.92.10:9000 \
  http://minio-{1...3}/data{1...2}
```
minio-2节点执行

```java
docker run -d --name minio \
  --restart=always --net=host \
  -e MINIO_ACCESS_KEY=minio \
  -e MINIO_SECRET_KEY=minio123 \
  -v minio-data1:/data1 \
  -v minio-data2:/data2 \
  minio/minio server \
  --address 192.168.92.11:9000 \
  http://minio-{1...3}/data{1...2}
```
minio-3节点执行

```java
docker run -d --name minio \
  --restart=always --net=host \
  -e MINIO_ACCESS_KEY=minio \
  -e MINIO_SECRET_KEY=minio123 \
  -v minio-data1:/data1 \
  -v minio-data2:/data2 \
  minio/minio server \
  --address 192.168.92.12:9000 \
  http://minio-{1...3}/data{1...2}
```
说明：docker部署集群模式时必须指定–net=host参数，使用主机网络，使用端口映射无法创建集群。

> 配置负载均衡 使用nginx和keepalived部署负载均衡，实际部署需要额外准备2个节点，这里作为测试复用minio集群前2个节点。
> 
> 在192.168.92.10及192.168.92.11节点执行以下操作：
> 
> 准备nginx配置文件，2个节点执行：

```java
mkdir -p /etc/nginx/conf.d
cat > /etc/nginx/conf.d/minio-lb.conf << 'EOF'
upstream minio_server {
    server 192.168.92.10:9000;
    server 192.168.92.11:9000;
    server 192.168.92.12:9000;
    }

server {
    listen 9001;
    server_name  localhost;
    ignore_invalid_headers off;
    client_max_body_size 0;
    proxy_buffering off;
    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_connect_timeout 300;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        chunked_transfer_encoding off;
        proxy_pass http://minio_server;
    }
}
EOF
```

## imageproxy

> 图片的缩放在日常开发中是一个挺有用的功能，已经有好多语言类库的实现了，但是基于proxy的模式会简单好多（高效，还能　cache。。。）
willnorris/imageproxy 是一个不错的实现，以下是一个简单的使用
-baseURL 添加minio的地址
-addr 0.0.0.0:8080 声明本地地址和端口
-cache 缓存

```java
docker run -d \
 -e "AWS_ACCESS_KEY_ID=minioadmin" \
 -e "AWS_SECRET_KEY=minioadmin" \
 -p 9001:8080 --name imageproxy willnorris/imageproxy \
 -baseURL http://192.168.1.50:9000/ \
 -addr 0.0.0.0:8080 -cache s3://us-east-1/demo/images?endpoint=192.168.1.50:9000&disableSSL=1&s3ForcePathStyle=1
```

> 正常使用
> 地址再加上需要显示图片的地址

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210508111415238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

> 配合minio使用
> 直接映射minio地址

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210508111500174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

> 换一下压缩参数

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210508111706846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## 安装nginx

```java
//先创建文件
mkdir -p /data/nginx/conf
mkdir -p /root/nginx/logs
mkdir -p /root/nginx/html
//启动一个测试的，用户复制文件出来
docker run  --name nginx -d -p 80:80 nginx
//查看下容器进程id
docker ps
//复制
//例子：47bc0ef981f5:/etc/nginx/nginx.conf  /data/nginx/conf/
docker cp 进程id:/etc/nginx/nginx.conf  /data/nginx/conf/

//临时关闭防火墙
setenforce 0
//启动
docker run -d --name nginx -p 80:80 --restart=always \
-v /data/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /root/nginx/logs:/var/log/nginx \
-v /root/nginx/html:/usr/share/nginx/html \
nginx

```
