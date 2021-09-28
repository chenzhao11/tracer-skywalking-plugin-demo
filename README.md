# 在 Spring 工程中上传数据到SkyWalking

这个例子是在官方[在Spring工程中使用SOFATracer](https://github.com/glmapper/tracer-zipkin-plugin-demo)的基础上修改的在Spring工程中上传数据到SkyWalking的例子。

## 前提条件

* Skywalking Server 可以根据官方的 [the user guide for OAP](https://github.com/apache/skywalking/blob/master/docs/en/setup/backend/docker.md) and [the user guide for UI](https://github.com/apache/skywalking/blob/master/docs/en/setup/backend/ui-setup.md#start-with-docker-image)来搭建。

##  引入插件

在这个例子中使用的是本地的jar包

```xml
        <dependency>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>tracer-sofa-boot-starter</artifactId>
            <version>3.1.1</version>
            <scope>system</scope>
            <systemPath>${pom.basedir}/src/main/resources/lib/tracer-sofa-boot-starter-3.1.1.jar
            </systemPath>
        </dependency>

        <dependency>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-tracer-skywalking-plugin</artifactId>
            <version>3.1.1</version>
            <scope>system</scope>
            <systemPath>${pom.basedir}/src/main/resources/lib/sofa-tracer-skywalking-plugin-3.1.1.jar
            </systemPath>
        </dependency>
        <dependency>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>tracer-core</artifactId>
            <version>3.1.1</version>
            <scope>system</scope>
            <systemPath>${pom.basedir}/src/main/resources/lib/tracer-core-3.1.1.jar</systemPath>
        </dependency>
        <dependency>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-tracer-springmvc-plugin</artifactId>
            <version>${sofa.tracer.version}</version>
        </dependency>
        <dependency>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-tracer-httpclient-plugin</artifactId>
            <version>${sofa.tracer.version}</version>
        </dependency>
        <!-- HttpClient Dependency -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpasyncclient</artifactId>
            <version>4.1.3</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.76</version>
        </dependency>
        <!-- opentracing dependency -->
        <dependency>
            <groupId>io.opentracing</groupId>
            <artifactId>opentracing-api</artifactId>
            <version>${opentracing.version}</version>
        </dependency>
        <dependency>
            <groupId>io.opentracing</groupId>
            <artifactId>opentracing-noop</artifactId>
            <version>${opentracing.version}</version>
        </dependency>
        <dependency>
            <groupId>io.opentracing</groupId>
            <artifactId>opentracing-mock</artifactId>
            <version>${opentracing.version}</version>
        </dependency>
        <dependency>
            <groupId>io.opentracing</groupId>
            <artifactId>opentracing-util</artifactId>
            <version>${opentracing.version}</version>
        </dependency>
```

> sofa-tracer-springmvc-plugin 是基于 标准 Servlet 实现的，因此即使是非 SpringMvc 工程，只要是标准的Servlet 工程，均可以使用该插件。


## 配置

这部分包括 filter 配置、配置文件配置、skywalking bean配置等。

### Filter 配置

在 web.xml 中配置 sofa-tracer-springmvc-plugin 插件的 Filter。 

```xml
  <filter>
    <filter-name>skywalkingFilter</filter-name>
    <filter-class>
      com.alipay.sofa.tracer.plugins.springmvc.SpringMvcSofaTracerFilter
    </filter-class>
  </filter>
  <filter-mapping>
    <filter-name>skywalkingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

### 配置文件

在 resources 目录下新建 sofa.tracer.properties 文件，并且增加 jaeger 上报所需的配置：

```properties
# Application Name
spring.application.name=SOFATracerSkyWalking
com.alipay.sofa.tracer.skywalking.enabled=true
com.alipay.sofa.tracer.skywalking.base-url=http://127.0.0.1:12800
com.alipay.sofa.tracer.skywalking.max-buffer-size=10000
```

### zipkin bean 配置

在 Spring 工程中，需要配置一个 bean，用于初始化 zipkin 上报所需的信息。

```xml
 <bean id="skywalkingReportRegisterBean" class="com.alipay.sofa.tracer.plugins.skywalking.initialize.SkywalkingReportRegisterBean"/>
```



## 启动 Jaeger server

按照前提条件中使用Docker启动相关服务后，正常启动后的主界面如下：

![image-20210928153359809](https://gitee.com/whutzhaochen/markdown/raw/master/img/20210928161716.png)



## 配置&启动tomcat

1、配置 Server

![image-20210928153439761](https://gitee.com/whutzhaochen/markdown/raw/master/img/20210928161726.png)

2、配置 Deployment

![image-20210928153449935](https://gitee.com/whutzhaochen/markdown/raw/master/img/20210928161729.png)

3、启动tomcat


## 访问资源&上报展示

1、访问资源

在浏览器中输入http://localhost:8089/tracer_skywalking_plugin_demo_war_exploded/hello

2、Jaeger展示

![image-20210928153539773](https://gitee.com/whutzhaochen/markdown/raw/master/img/20210928161732.png)
