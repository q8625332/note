

```
FROM livekit/livekit-server

# 复制配置文件
COPY livekit.yaml /livekit.yaml

# 暴露端口（7880 改为 7860）
EXPOSE 7860 7881 7882/udp

# 传递配置参数
CMD ["--config", "/livekit.yaml", "--node-ip=127.0.0.1"]

```
