
```
# 使用 New API 官方镜像
FROM calciumion/new-api:latest

# 让 New API 在 HuggingFace 期望的端口上监听（默认 7860）
ENV PORT=7860

# 设置时区，可按需修改
ENV TZ=Asia/Shanghai

# 不需要再写 CMD，直接继承镜像默认启动方式
# 如需调试，可以改成：
# CMD ["./new-api"]
```
