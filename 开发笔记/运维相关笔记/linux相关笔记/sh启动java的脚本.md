
>注意如果在win上编译过文件，在linux上的格式会异常，所以需要转换一下。

```linux
#安装转换器，在基于 Debian 的系统上，例如 Ubuntu，你可以使用以下命令安装：
sudo apt-get install dos2unix
#安装转换器，在基于 Red Hat 的系统上，例如 CentOS，你可以使用以下命令安装：
sudo yum install dos2unix

#转换
dos2unix start.sh
```

**start.sh**

```sh
#!/bin/bash

# 设置 Java 应用程序的相关参数
APP_NAME="gateway-0.0.1-SNAPSHOT"
JAVA_HOME="java -jar"
JAR_PATH=$APP_NAME.jar
LOG_FILE=$APP_NAME.log
PID_FILE=$APP_NAME.pid

# JVM 参数
JAVA_OPTS="-Xms256m -Xmx1024m"

# 检查是否已经有正在运行的实例
if [ -f "$PID_FILE" ] && kill -0 $(cat "$PID_FILE"); then
    echo "服务已经在运行中 (PID: $(cat $PID_FILE))"
    exit 1
fi

# 启动服务
echo "启动服务..."
nohup $JAVA_HOME $JAVA_OPTS -jar $JAR_PATH > $LOG_FILE 2>&1 &

# 获取新启动的服务的 PID 并保存到 PID 文件
echo $! > $PID_FILE
echo "服务启动成功 (PID: $!)"
```

**stop.sh**

```sh
#!/bin/bash

# 设置 PID 文件的路径
APP_NAME="gateway-0.0.1-SNAPSHOT"
PID_FILE=$APP_NAME.pid

# 检查 PID 文件是否存在
if [ ! -f "$PID_FILE" ]; then
    echo "PID 文件不存在，服务可能未启动"
    exit 1
fi

# 获取 PID 并关闭服务
PID=$(cat "$PID_FILE")
echo "停止服务 (PID: $PID)..."
kill $PID

# 检查服务是否成功停止
if kill -0 $PID 2>/dev/null; then
    echo "服务未能停止，请手动检查"
else
    echo "服务已成功停止"
    rm -f "$PID_FILE"
fi
```

**log.sh**

```sh
#!/bin/bash

# 设置日志文件的路径
APP_NAME="gateway-0.0.1-SNAPSHOT"
LOG_FILE=$APP_NAME.log

# 检查日志文件是否存在
if [ ! -f "$LOG_FILE" ]; then
    echo "日志文件不存在：$LOG_FILE"
    exit 1
fi

# 使用 tail 命令查看日志文件的内容
echo "显示日志文件内容：$LOG_FILE"
tail -f $LOG_FILE
```