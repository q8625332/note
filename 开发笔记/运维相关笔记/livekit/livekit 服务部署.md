#### 拉取生成文件镜像

```
sudo docker pull livekit/generate
```
#### 生成配置文件

```
正常使用的命令：
docker run --rm -it -v$PWD:/output livekit/generate
或者（本地部署命令，毕竟简单）：
sudo docker run --rm -v $PWD:/output livekit/generate --local



输出（本地部署）：
Generated livekit.yaml that's suitable for local testing

Start LiveKit with:
docker run --rm \
    -p 7880:7880 \
    -p 7881:7881 \
    -p 7882:7882/udp \
    -v $PWD/livekit.yaml:/livekit.yaml \
    livekit/livekit-server \
    --config /livekit.yaml \
    --node-ip=127.0.0.1

Note: --node-ip needs to be reachable by the client. 127.0.0.1 is accessible only to the current machine

Server URL:  ws://localhost:7880
API Key: APIoWyzeFdn3WQh
API Secret: dlLp8hWd47e7MmeVCIt7O1XdmmHdjpHl9APteBIX9v0B

Here's a test token generated with your keys: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3OTkwMTM2MzMsImlzcyI6IkFQSW9XeXplRmRuM1dRaCIsIm5hbWUiOiJUZXN0IFVzZXIiLCJuYmYiOjE3NjMwMTM2MzMsInN1YiI6InRlc3QtdXNlciIsInZpZGVvIjp7InJvb20iOiJteS1maXJzdC1yb29tIiwicm9vbUpvaW4iOnRydWV9fQ.TOHiLxYzrtVefWRpuhDnvZG1kunmwpSIlPuW02ULnUU

An access token identifies the participant as well as the room it's connecting to

(记住输出信息，因为运行和对接livekit时候要用)
```
#### 拉取livekit/livekit-server

```
sudo docker pull livekit/livekit-server
```
#### 开始运行服务

```
sudo docker run -dit -p 7880:7880 -p 7881:7881 -p 7882:7882/udp -v $PWD/livekit.yaml:/livekit.yaml livekit/livekit-server --config /livekit.yaml --node-ip=0.0.0.0
```
