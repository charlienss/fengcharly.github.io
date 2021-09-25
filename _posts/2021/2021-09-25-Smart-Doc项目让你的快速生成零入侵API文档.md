---
title: Smart-Doc项目让你的快速生成零入侵API文档
categories:
 - Java后端
tags:
 - 后端开发
description: 开发小伙伴们对API文档想必非常的熟悉了，不管是用公司单独维护的文档工具，还是使用比较多的Swagger，这类文档生成工具对代码的侵入性强，在后期的修改和维护带来不少的烦恼...

---

开发小伙伴们对API文档想必非常的熟悉了，不管是用公司单独维护的文档工具，还是使用比较多的Swagger，这类文档生成工具对代码的侵入性强，在后期的修改和维护带来不少的烦恼。

今天就给大家推荐一款对代码零入侵，同时支持JAVA REST API和Apache Dubbo RPC接口文档生成的工具，在业内率先提出基于JAVA泛型定义推导的理念， 完全基于接口源码来分析生成接口文档，不采用任何注解侵入到业务代码中。你只需要按照java-doc标准编写注释， 就能帮你生成一个简易明了的Markdown、HTML5、Postman Collection2.0+、OpenAPI 3.0+的文档。

这个项目的名字叫做smart-doc，在gitee上已有1.8K+的start，项目代码仓库地址如下：

```java
https://gitee.com/smart-doc-team/smart-doc
```

![hBqjm9.png](https://z3.ax1x.com/2021/09/01/hBqjm9.png)

另外smart-doc可以和Torna搭配使用，使用smart-doc无侵入完成Java源代码分析和提取注释生成API文档，自动将文档推送到Torna企业级接口文档管理平台。

[![hBOi40.png](https://z3.ax1x.com/2021/09/01/hBOi40.png)](https://imgtu.com/i/hBOi40)

#### 1.smart-doc特性:

- 零注解、零学习成本、只需要写标准JAVA注释。
- 基于源代码接口定义自动推导，强大的返回结构推导。
- 支持Spring MVC、Spring Boot、Spring Boot Web Flux(controller书写方式)、Feign。
- 支持Callable、Future、CompletableFuture等异步接口返回的推导。
- 支持JavaBean上的JSR303参数校验规范，包括分组验证。
- 对JSON请求参数的接口能够自动生成模拟JSON参数。
- 对一些常用字段定义能够生成有效的模拟值。
- 支持生成JSON返回值示例。
- 支持从项目外部加载源代码来生成字段注释(包括标准规范发布的jar包)。
- 支持生成多种格式文档：Markdown、HTML5、Asciidoctor、Postman Collection、OpenAPI 3.0。 Up- 开放文档数据，可自由实现接入文档管理系统。
- 支持导出错误码和定义在代码中的各种字典码到接口文档。
- 支持Maven、Gradle插件式轻松集成。
- 支持Apache Dubbo RPC接口文档生成。
- debug接口调试html5页面完全支持文件上传，下载(@download tag标记下载方法)测试。

#### 2.使用方法

smart-doc可以很方便的和Spring Boot结合，你只需要在项目中添加如下的Maven插件即可：

```java
<plugin>
    <groupId>com.github.shalousun</groupId>
    <artifactId>smart-doc-maven-plugin</artifactId>
    <version>[最新版本]</version>
    <configuration>
        <!--指定生成文档的使用的配置文件,配置文件放在自己的项目中-->
        <configFile>./src/main/resources/smart-doc.json</configFile>
        <!--指定项目名称-->
        <projectName>测试</projectName>
        <!--smart-doc实现自动分析依赖树加载第三方依赖的源码，如果一些框架依赖库加载不到导致报错，这时请使用excludes排除掉-->
        <excludes>
            <!--格式为：groupId:artifactId;参考如下-->
            <exclude>com.alibaba:fastjson</exclude>
        </excludes>
        <!--自1.0.8版本开始，插件提供includes支持,配置了includes后插件会按照用户配置加载而不是自动加载，因此使用时需要注意-->
        <!--smart-doc能自动分析依赖树加载所有依赖源码，原则上会影响文档构建效率，因此你可以使用includes来让插件加载你配置的组件-->
        <includes>
            <!--格式为：groupId:artifactId;参考如下-->
            <!--也可以支持正则式如：com.alibaba:.* -->
            <include>com.alibaba:fastjson</include>
        </includes>
    </configuration>
    <executions>
        <execution>
            <!--如果不需要在执行编译时启动smart-doc，则将phase注释掉-->
            <phase>compile</phase>
            <goals>
                <!--smart-doc提供了html、openapi、markdown等goal，可按需配置-->
                <goal>html</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

#### 3.配置项

在项目中添加创建一个`smart-doc.json`配置文件，插件读取这个配置来生成项目的文档， 这个配置内容实际上就是以前采用单元测试编写的`ApiConfig`转成json后的结果，因此关于配置项说明可以参考原来单元测试的配置。

**最小配置单元：**

```
{
   "outPath": "D://md2" //指定文档的输出路径,相对路径时请用./开头，eg:./src/main/resources/static/doc
}
```

> 如果你想把html文档也打包到应用中一起访问，则建议你配置路径为：src/main/resources/static/doc

仅仅需要上面一行配置就能启动smart-doc-maven-plugin插件，详细配置说明可以参考官方文档。

添加好插件和配置文件后可以直接运行Maven命令生成文档。

```java
//生成html
mvn -Dfile.encoding=UTF-8 smart-doc:html
//生成markdown
mvn -Dfile.encoding=UTF-8 smart-doc:markdown
//生成adoc
mvn -Dfile.encoding=UTF-8 smart-doc:adoc
```

当然如果嫌手动输入命令比较麻烦，还可以使用IDEA的界面进行操作。

[![hBxKsO.png](https://z3.ax1x.com/2021/09/01/hBxKsO.png)](https://imgtu.com/i/hBxKsO)

#### 4.生成文档后的效果图

可以通过上面构建生成的Html、MarkDown等格式的文件进行浏览，界面排版清晰，一点都不输Swagger。

[![hBxok9.png](https://z3.ax1x.com/2021/09/01/hBxok9.png)](https://imgtu.com/i/hBxok9)

[![hBzmkj.png](https://z3.ax1x.com/2021/09/01/hBzmkj.png)](https://imgtu.com/i/hBzmkj)

##### 