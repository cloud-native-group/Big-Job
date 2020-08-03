# 第九组报告

组员：171870635 陈凯悦，181250172 杨沛鑫，181250152 魏荣来

## 一、功能要求

### 1.实现接口

首先创建**eureka-server**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.3.1.RELEASE</version>
      <relativePath/> <!-- lookup parent from repository -->
   </parent>
   <groupId>io.daocloud</groupId>
   <artifactId>eureka-server</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   <name>eureka-server</name>
   <description>Demo project for Spring Boot</description>

   <properties>
      <java.version>1.8</java.version>
      <spring-cloud.version>Hoxton.SR6</spring-cloud.version>
   </properties>

   <dependencies>
      <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
      </dependency>

<!--      <dependency>-->
<!--         <groupId>org.springframework.boot</groupId>-->
<!--         <artifactId>spring-boot-starter-test</artifactId>-->
<!--         <scope>test</scope>-->
<!--         <exclusions>-->
<!--            <exclusion>-->
<!--               <groupId>org.junit.vintage</groupId>-->
<!--               <artifactId>junit-vintage-engine</artifactId>-->
<!--            </exclusion>-->
<!--         </exclusions>-->
<!--      </dependency>-->
   </dependencies>

   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>

   <build>
      <plugins>
         <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
         </plugin>
      </plugins>
   </build>

</project>
```

配置文件

```properties
eureka.client.service-url.defaultZone=http://localhost:8080/eureka
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

启动类

```java
package io.daocloud.eurekaserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

   public static void main(String[] args) {
      SpringApplication.run(EurekaServerApplication.class, args);
   }

}
```

然后创建微服务**rest-service**，依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.daocloud</groupId>
    <artifactId>rest-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>rest-service</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR6</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

配置文件

```properties
spring.application.name=rest-service
server.port=7070

eureka.client.service-url.defaultZone=http://localhost:8080/eureka/
```

控制层

```java
package com.daocloud.restservice.controller;


import com.daocloud.restservice.service.HelloService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@RestController
public class HelloController {

    @Autowired
    private HelloService service;

    @GetMapping("/")
    public Map<String, String> sayHello(){
        return service.sayHello();
    }

}
```

服务层

```java
package com.daocloud.restservice.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.Map;

@Service
public class HelloService {

    @Value("${server.port}")
    private String port;

    public Map<String,String> sayHello(){
        Map<String, String> map = new HashMap<>();
        map.put("message","hello");
        map.put("port",port);
        return map;
    }
}
```

启动类

```java
package com.daocloud.restservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class RestServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(RestServiceApplication.class, args);
    }

}
```

### 2.使用zuul实现url粒度的限流

导入依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.daocloud</groupId>
    <artifactId>zuul-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>zuul-service</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR6</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
        <dependency>
            <groupId>com.marcosbarbero.cloud</groupId>
            <artifactId>spring-cloud-zuul-ratelimit</artifactId>
            <version>2.0.6.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

配置

```yml
spring:
    application:
        name: zuul-service
server:
    port: 6060
eureka:
    client:
        serviceUrl:
            defaultZone: http://10.64.140.215:8969/eureka/
zuul:
    routes:
        rest-service:
            path: /rest/**
#            serviceId: rest-service
            url: http://rest-service:7070

    ratelimit:
        #    key-prefix: springcloud-book #按粒度拆分的临时变量key前缀
        enabled: true #启用开关
        #    repository: IN_MEMORY #key存储类型，默认是IN_MEMORY本地内存，此外还有多种形式
        #    behind-proxy: true #表示代理之后
        default-policy: #全局限流策略，可单独细化到服务粒度
            limit: 100 #在一个单位时间窗口的请求数量
            #      quota: 0 #在一个单位时间窗口的请求时间限制
            refresh-interval: 1 #单位时间窗口
            type:
                #        - user #可指定用户粒度
                #        - origin #可指定客户端地址粒度
                - url #可指定url粒度
```

以上配置表明，当请求达到每秒 100 次的时候，返回 429（Too many requests），即使微服务启动了多个实例，仍然如此，即多个实例接口的每秒请求次数之和不超过100次。

启动类

```java
package com.daocloud.zuulservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy
public class ZuulServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(ZuulServiceApplication.class, args);
    }

}
```

### 3.测试

#### （1）使用postman测试接口，负载均衡

测试端口

```javascript
pm.test("port check", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.port).to.eql("7070");
});
```

测试结果

![2020-07-24_232642.png](pic\2020-07-24_232642.png)

由此可见，zuul的负载均衡有效

#### （2）使用Apachebench测试限流

![2020-07-24_232946.png](pic\2020-07-24_232946.png)

使用apachebench，发送200个请求，在0.615秒完成，有100个请求失败，表明zuul的限流是成功的。

## 二、DevOps 要求



### 1. 项目的 Dockerfile和 K8s 编排文件

#### (1) eureka-server

Dockerfile文件

```dockerfile
FROM openjdk:16-slim
ADD ./target/eureka-server.jar /app/eureka-server.jar
ADD runboot.sh /app/
WORKDIR /app
RUN chmod a+x runboot.sh
CMD /app/runboot.sh

```

runboot.sh

```sh
java ${JAVA_OPS} -Duser.timezone=Asia/Shanghai -Djava.security.egd=file:/dev/./urandom -jar /app/eureka-server.jar
```

#### (2) rest-service

Dockerfile文件

```dockerfile
FROM openjdk:16-slim
ADD ./target/rest-service.jar /app/rest-service.jar
ADD runboot.sh /app/
WORKDIR /app
RUN chmod a+x runboot.sh
CMD /app/runboot.sh


```

runboot.sh

```sh
java ${JAVA_OPS} -Duser.timezone=Asia/Shanghai -Djava.security.egd=file:/dev/./urandom -jar /app/rest-service.jar
```

#### (3) zuul-service

Dockerfile文件

```dockerfile
FROM openjdk:16-slim
ADD ./target/zuul-service.jar /app/zuul-service.jar
ADD runboot.sh /app/
WORKDIR /app
RUN chmod a+x runboot.sh
CMD /app/runboot.sh


```

runboot.sh

```sh
java ${JAVA_OPS} -Duser.timezone=Asia/Shanghai -Djava.security.egd=file:/dev/./urandom -jar /app/zuul-service.jar
```



### 2. jenkins 流水线（集成和部署）

#### (1) eureka-server

pipeline script

```Pipeline script
pipeline {
    agent none
    stages {
		stage('Clone to master') {
			agent {
			label 'master'
			}
			steps {
				echo "1.Git Clone Stage"
				git url: "https://github.com/cloud-native-group/eureka-server-demo.git"
			}
		}
		stage('Maven Build') {
			agent {
				docker {
				image 'maven:latest'
				args '-v /root/.m2:/root/.m2'
				}
			}
			steps {
				echo "2.Maven Build Stage"
				sh 'mvn -B clean package -Dmaven.test.skip=true'
			}
		}
		stage('Image Build') {
			agent {
				label 'master'
			}
			steps {
				echo "3.Image Build Stage"
				sh 'docker build -f Dockerfile --build-arg jar_name=target/eureka-sever.jar -t eureka-server:${BUILD_ID} . '
				sh 'docker tag	eureka-server:${BUILD_ID} harbor.edu.cn/cn202009/eureka-server:${BUILD_ID}'
			}
		}
		stage('Push') {
			agent {
				label 'master'
			}
			steps {
				echo "4.Push Docker Image Stage"
				sh "docker login --username=cn202009 harbor.edu.cn -p cn202009"
				sh "cat ~/.docker/config.json |base64 -w 0"
				sh "docker push harbor.edu.cn/cn202009/eureka-server:${BUILD_ID}"
			}
		}
    }
}

node('slave') {
    container('jnlp-kubectl') {

        stage('Git Cone') {
            git url: "https://github.com/cloud-native-group/eureka-server-demo.git"
        }
        
        stage('YAML') {
            echo "5. Change YAML File Stage"
            sh 'sed -i "s#{VERSION}#${BUILD_ID}#g" ./jenkins/scripts/eureka-server.yaml'
        }
        
        stage('Deploy') {
            echo "5. Deploy To K8s Stage"
			sh 'kubectl apply -f ./jenkins/scripts/mytoken.yaml -n cn202009'
            sh 'kubectl apply -f ./jenkins/scripts/eureka-server.yaml -n cn202009'
        }
    }
}
```

![image-20200803015813780](pic\image-20200804004217081.png)



![image-20200804004335141](pic\image-20200804004335141.png)



#### (2) rest-service

pipeline script

```Pipeline script
pipeline {
    agent none
    stages {
		stage('Clone to master') {
			agent {
			label 'master'
			}
			steps {
				echo "1.Git Clone Stage"
				git url: "https://github.com/cloud-native-group/rest-service-demo.git"
			}
		}
		stage('Maven Build') {
			agent {
				docker {
				image 'maven:latest'
				args '-v /root/.m2:/root/.m2'
				}
			}
			steps {
				echo "2.Maven Build Stage"
				sh 'mvn -B clean package -Dmaven.test.skip=true'
			}
		}
		stage('Image Build') {
			agent {
				label 'master'
			}
			steps {
				echo "3.Image Build Stage"
				sh 'docker build -f Dockerfile --build-arg jar_name=target/rest-service.jar -t rest-service:${BUILD_ID} . '
				sh 'docker tag	rest-service:${BUILD_ID} harbor.edu.cn/cn202009/rest-service:${BUILD_ID}'
			}
		}
		stage('Push') {
			agent {
				label 'master'
			}
			steps {
				echo "4.Push Docker Image Stage"
				sh "docker login --username=cn202009 harbor.edu.cn -p cn202009"
				sh "cat ~/.docker/config.json |base64 -w 0"
				sh "docker push harbor.edu.cn/cn202009/rest-service:${BUILD_ID}"
			}
		}
    }
}

node('slave') {
    container('jnlp-kubectl') {

        stage('Git Cone') {
            git url: "https://github.com/cloud-native-group/rest-service-demo.git"
        }
        
        stage('YAML') {
            echo "5. Change YAML File Stage"
            sh 'sed -i "s#{VERSION}#${BUILD_ID}#g" ./jenkins/scripts/rest-service.yaml'
        }
        
        stage('Deploy') {
            echo "5. Deploy To K8s Stage"
			sh 'kubectl apply -f ./jenkins/scripts/mytoken.yaml -n cn202009'
            sh 'kubectl apply -f ./jenkins/scripts/rest-service.yaml -n cn202009'
        }
    }
}
```

![image-20200803015831369](pic\image-20200804004217081.png)



![image-20200803020141495](pic\image-20200803020141495.png)



#### (3) zuul-service

pipeline script

```Pipeline script
pipeline {
    agent none
    stages {
		stage('Clone to master') {
			agent {
			label 'master'
			}
			steps {
				echo "1.Git Clone Stage"
				git url: "https://github.com/cloud-native-group/zuul-service-demo.git"
			}
		}
		stage('Maven Build') {
			agent {
				docker {
				image 'maven:latest'
				args '-v /root/.m2:/root/.m2'
				}
			}
			steps {
				echo "2.Maven Build Stage"
				sh 'mvn -B clean package -Dmaven.test.skip=true'
			}
		}
		stage('Image Build') {
			agent {
				label 'master'
			}
			steps {
				echo "3.Image Build Stage"
				sh 'docker build -f Dockerfile --build-arg jar_name=target/zuul-service.jar -t zuul-service:${BUILD_ID} . '
				sh 'docker tag	zuul-service:${BUILD_ID} harbor.edu.cn/cn202009/zuul-service:${BUILD_ID}'
			}
		}
		stage('Push') {
			agent {
				label 'master'
			}
			steps {
				echo "4.Push Docker Image Stage"
				sh "docker login --username=cn202009 harbor.edu.cn -p cn202009"
				sh "cat ~/.docker/config.json |base64 -w 0"
				sh "docker push harbor.edu.cn/cn202009/zuul-service:${BUILD_ID}"
			}
		}
    }
}

node('slave') {
    container('jnlp-kubectl') {

        stage('Git Cone') {
            git url: "https://github.com/cloud-native-group/zuul-service-demo.git"
        }
        
        stage('YAML') {
            echo "5. Change YAML File Stage"
            sh 'sed -i "s#{VERSION}#${BUILD_ID}#g" ./jenkins/scripts/zuul-service.yaml'
        }
        
        stage('Deploy') {
            echo "5. Deploy To K8s Stage"
			sh 'kubectl apply -f ./jenkins/scripts/mytoken.yaml -n cn202009'
            sh 'kubectl apply -f ./jenkins/scripts/zuul-service.yaml -n cn202009'
        }
    }
}
```

![image-20200804004217081](pic\image-20200804004217081.png)



![image-20200804004105287](pic\image-20200804004105287.png)



图没有换新的

## 三、扩容场景