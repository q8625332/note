
## 问题描述

我们从互联网上拉取一个若依或者pigx的框架，在没有开发的情况下，会发现包的体积已经来到了百兆，随着业务逻辑的增加，引用的包越来越多，会发现这个jar包的体积会越来越不可控，因为，缩小包的体积就成了重中之重。

## 原因分析：

首先要先分析为什么springboot的包为什么会这么大，也就是springboot的打包机制
Spring Boot应用打包机制充分利用了Maven或Gradle构建工具的强大功能，旨在简化传统Java应用的构建与部署流程。其核心在于创建一个可执行的Fat JAR，使得开发者能够轻松地将整个Spring Boot应用及其依赖项打包成单个文件，从而实现一键启动和便捷部署。

## mavem配置

> 简单描述一下打包的配置。
> includes 需要保留的jar包。一般会选择变动比较大的包。试试需要maven更新的包就需要放到这个地方。
> maven-dependency-plugin这个打包是将包分离出去。
> outputDirectory 这个指定分离出去的包需要放哪里。

```xml
<build>  
    <plugins>  
        <plugin>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-maven-plugin</artifactId>  
            <version>${spring-boot.version}</version>  
			<configuration>  
				<!--启动类配置-->  
				<mainClass>com.ljq.auth.AuthApplication</mainClass>  
				<!--解决windows命令行窗口中文乱码-->  
				<jvmArguments>-Dfile.encoding=UTF-8</jvmArguments>  
				<!-- 瘦包 -->  
				<layout>ZIP</layout>  
				<!--保留的包-->  
				<includes>  
					<include>  
						<groupId>com.ljq</groupId>  
						<artifactId>common</artifactId>  
					</include>  
					<include>  
						<groupId>com.ljq</groupId>  
						<artifactId>framework-database-starter</artifactId>  
					</include>  
					<include>  
						<groupId>com.ljq</groupId>  
						<artifactId>>framework-redis-starter</artifactId>  
					</include>  
					<include>  
						<groupId>com.ljq</groupId>  
						<artifactId>framework-security-starter</artifactId>  
					</include>  
				</includes>  
				<excludes>  
					<exclude>  
						<groupId>org.projectlombok</groupId>  
						<artifactId>lombok</artifactId>  
					</exclude>  
				</excludes>  
			</configuration>  
        </plugin>  
        <!--外包指定-->  
        <plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-dependency-plugin</artifactId>  
            <executions>  
                <execution>  
                    <id>copy-dependencies</id>  
                    <phase>package</phase>  
                    <goals>  
                        <goal>copy-dependencies</goal>  
                    </goals>  
                    <configuration>  
                        <outputDirectory>${project.build.directory}/lib</outputDirectory>  
                        <!--是否排除传递性-->  
                        <excludeTransitive>false</excludeTransitive>  
                        <!--是否去掉 jar 包版本信息-->  
                        <stripVersion>false</stripVersion>  
                        <!--包含范围-->  
                        <includeScope>runtime</includeScope>  
                    </configuration>  
                </execution>  
            </executions>  
        </plugin>  
    </plugins>  
</build>
```

## jar包启动命令

> 启动的时候需要指定包的路径

```
java -jar -Dloader.path="./lib" javaFileName.jar
```