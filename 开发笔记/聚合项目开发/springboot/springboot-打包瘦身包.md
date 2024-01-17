springboot 的打包插件默认打的是fat jar包，也就是将所有依赖包都打包到jar包里，这就导致jar包非常的大，几十甚至上百M都有，在外网部署时如果网速慢，则文件上传就要耗掉很长的时间，这在紧急更新时是很难接受的，因此为减少部署项目包的文件大小，加快部署时的网络传输速度，在开发项目中采用spring-boot-thin-launcher 打包插件进行打包，关于此插件可[参考项目官网](https://github.com/spring-projects-experimental/spring-boot-thin-launcher)，它在打包时，不会将依赖包打包进JAR包里，在启动APP时采用Maven的机制实时下载依赖包，从而减少包的体积，但也因此不采用脱机模式时会导致第一次启动时非常的久（下载依赖包）！
要启用此插件，在pom.xml文件里进行如下配置：

```java
<build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot.version}</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.springframework.boot.experimental</groupId>
                        <artifactId>spring-boot-thin-layout</artifactId>
                        <version>1.0.26.RELEASE</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
```
经此配置后，在package时打包出来的就是经瘦身后的JAR包，启动时通过以下方式启动项目

```java
java -Dthin.root=. -jar app-0.0.1-SNAPSHOT.jar
```
注意，如果启动时报找不到org.springframework.boot.loader.wrapper.ThinJarWrapper启动类，则是因为在目录(thin.root）下的repository目录下缺少 spring-boot-thin-launcher-1.0.26.RELEASE.jar 包，详细路径如下

```java
org/springframework/boot/experimental/spring-boot-thin-launcher/1.0.26.RELEASE/spring-boot-thin-launcher-1.0.26.RELEASE.jar
```
如果希望能直接脱机使用或者减少第一次启动时下载依赖包的时间，可以在pom.xml文件里配置一下另外一个插件，以便在打包时能一并将依赖包分离出来，部署时一并部署即可

```java
<build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot.version}</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.springframework.boot.experimental</groupId>
                        <artifactId>spring-boot-thin-layout</artifactId>
                        <version>1.0.26.RELEASE</version>
                    </dependency>
                </dependencies>
            </plugin>
            <!--在打包时下载依赖包 -->
            <plugin>
                <groupId>org.springframework.boot.experimental</groupId>
                <artifactId>spring-boot-thin-maven-plugin</artifactId>
                <version>1.0.26.RELEASE</version>
                <executions>
                    <execution>
                        <id>package</id>
                        <goals>
                            <goal>resolve</goal>
                        </goals>
                        <inherited>false</inherited>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```
这样打包时会在target目录多出 thin/root/ 目录，里面会有repository目录及瘦身JAR包文件，repository里面就是分离出来的依赖包，部署时将此目录放在thin.root指定的目录下即可。

spring-boot-thin-launcher  插件默认采用的maven仓库是https://repo.spring.io/libs-snapshot, 如果项目中用到了内网仓库，则需要在thin.root配置的目录下建立.m2/settings.xml文件，里面配置maven的仓库信息即可。

采用此方式部署项目时，在Docker里统一约定thin.root参数值为 /im-home/thin 也就是Dockerfile的配置如下：

```java
ENV EXT_OPTIONS "-Dthin.root=/im-home/thin“
ENV JAVA_OPTIONS "-Dfile.encoding=UTF-8 $EXT_OPTIONS xxxxxxxx“
```
