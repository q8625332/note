> 安装grafana
> 暂时未写

## 安装promtail
> docker安装
> /path/to/promtail-config.yaml 是系统路径的配置文件

/path/to/promtail-config.yaml


```linux
docker run -d --name=promtail \
 -v /path/to/promtail-config.yaml:/etc/promtail/config.yml \
 -v /hytto/deploy/gfw/rpc_services/logs/:/hytto/deploy/gfw/rpc_services/logs/ \
 -p 9080:9080 \
 grafana/promtail:2.6.1 \
 -config.file=/etc/promtail/config.yml
```
