## redis.yml

```java
spring:
  data:
    redis:
      #  Redis默认情况下有16个分片，这里配置具体使用的分片，默认是0
      database: 0
      host: 192.168.196.129
      timeout: 10000ms
      port: 31255
      lettuce:
        pool:
          # 连接池最大连接数（使用负值表示没有限制） 默认 8
          max-active: 300
          # 连接池中的最大空闲连接 默认 8
          max-idle: 100
          # 连接池中的最小空闲连接 默认 0
          min-idle: 0
          # 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
          max-wait: -1ms
```
## resource-config.yml

```java
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://192.168.196.129:32104/auth/oauth2/jwks
```
## minio-config.yml

```java
minio:
  host: http://192.168.196.129:30501
  url: ${minio.host}/${minio.bucket}/
  access-key: openpitrixminioaccesskey
  secret-key: openpitrixminiosecretkey
  bucket: ljq
#自定义的配置
minio-config:
  accessAddress: ${minio.host}
  uploadAddress: ${minio.host}
  accessKey: ${minio.access-key}
  secretKey: ${minio.secret-key}
```
