---
title: "使用 Spring Boot Layers 构建 Docker 层"
date: 2023-04-27T17:47:05+08:00
tags:
- spring
- docker
categories:
- ops
draft: false

---
## 介绍

从 `2.3.0` 开始，spring boot 提供了新的打包方式，在打 fat jar 时可以将依赖，代码模块打进不同的目录中，因此
可以在构建 docker 镜像时可以根据依赖的的更新频率将依赖分入不同目录中，进而构建到不同的层中，这样可以减少，版本
构建时，发生变化的 docker 镜像层的体积。

## spring boot 配置

- pom.xml 添加配置

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <layers>
            <enabled>true</enabled>
        </layers>
    </configuration>
</plugin>
```

- 打包生成的 jar 结构

```yaml
- "dependencies":
  - "BOOT-INF/lib/"
- "spring-boot-loader":
  - "org/"
- "snapshot-dependencies":
- "application":
  - "BOOT-INF/classes/"
  - "BOOT-INF/classpath.idx"
  - "BOOT-INF/layers.idx"
  - "META-INF/"
```

    - `dependencies` 为第三方依赖
    - `snapshot-dependencies`  版本中包含 SNAPSHOT 的第三方依赖
    - `spring-boot-loader` spring loader 类
    - `application` 应用代码及资源

- 解压 jar

查看 jar 包内容

```sh
java -Djarmode=layertools -jar demo-0.0.1.jar list
```

解压 jar 包到当前目录

```sh
java -Djarmode=layertools -jar demo-0.0.1.jar extract
```

## 构建 docker 镜像

```Dockerfile
FROM jdk:11

WORKDIR /app

COPY dependencies/ ./
COPY snapshot-dependencies/ ./
COPY spring-boot-loader/ ./
COPY application/ ./
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

## 参数资料

- https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/#packaging.layers
- https://www.baeldung.com/docker-layers-spring-boot
- https://github.com/eugenp/tutorials/tree/master/docker-modules/docker-spring-boot
- https://www.baeldung.com/jib-dockerizing