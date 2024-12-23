
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

****