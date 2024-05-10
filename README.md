黑马的这个项目有一些需要注意的地方:
1、实战8和9的部分的实现，后面的课程中需要用到。如果不一致会很麻烦
2、项目图片较多失效
3、给的前端页面不完整，并且没有源代码  ,比如管理端页面中查看评论等部分会提示演示用页面，没有具体实现(大致是这个，不想启动项目了hh)

但整体上面确实能学到很多东西，这个项目用到的技术栈比较多，而且有一些比较有难度.



下面是Nacos的配置(将容器导出为镜像太大了):

leadnews-user 

```
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/leadnews_user?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false
    username: root
    password: 123456
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
# 设置Mapper接口所对应的XML文件位置，如果你在Mapper接口中有自定义方法，需要进行该配置
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.user.pojos
```

leadnews-app-gateway

```
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true
        corsConfigurations:
          '[/**]':
            allowedHeaders: "*"
            allowedOrigins: "*"
            allowedMethods:
              - GET
              - POST
              - DELETE
              - PUT
              - OPTION
      routes:
        # 平台管理
        - id: user
          uri: lb://leadnews-user
          predicates:
            - Path=/user/**
          filters:
            - StripPrefix= 1
        #文章微服务
        - id: article   
          uri: lb://leadnews-article
          predicates:
            - Path=/article/**
          filters:
            - StripPrefix= 1
        - id: leadnews-search
          uri: lb://leadnews-search
          predicates:
            - Path=/search/**
          filters:
            - StripPrefix= 1
        - id: leadnews-behavior
          uri: lb://leadnews-behavior
          predicates:
            - Path=/behavior/**
          filters:
            - StripPrefix= 1
        - id: leadnews-comment
          uri: lb://leadnews-comment
          predicates:
            - Path=/comment/**
          filters:
            - StripPrefix= 1
```

leadnews-article

```
spring:
  data:
    mongodb:
      host: 192.168.137.127
      port: 27017
      database: leadnews-comment
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/leadnews_article?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false
    username: root
    password: 123456
  kafka:
    bootstrap-servers: 192.168.137.127:9092
    producer:
      retries: 10
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      group-id: ${spring.application.name}
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
# 设置Mapper接口所对应的XML文件位置，如果你在Mapper接口中有自定义方法，需要进行该配置
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.article.pojos
minio:
  accessKey: minio
  secretKey: minio123
  bucket: leadnews
  endpoint: http://192.168.137.127:9000
  readPath: http://192.168.137.127:9000
xxl:
  job:
    admin:
      addresses: http://192.168.137.127:8888/xxl-job-admin
    executor:
      #appname: xxl-job-executor-sample
      appname: leadnews-hot-article-executor  #分片
      port: ${executor.port:9999}
kafka:
  hosts: 192.168.137.127:9092
  group: ${spring.application.name}
```

leadnews-wemedia

```
spring:
  data:
    mongodb:
      host: 192.168.137.127
      port: 27017
      database: leadnews-comment
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/leadnews_wemedia?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false
    username: root
    password: 123456
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
  kafka:
    bootstrap-servers: 192.168.137.127:9092
    producer:
      retries: 10
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
# 设置Mapper接口所对应的XML文件位置，如果你在Mapper接口中有自定义方法，需要进行该配置
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.media.pojos
minio:
  accessKey: minio
  secretKey: minio123
  bucket: leadnews
  endpoint: http://192.168.137.127:9000
  readPath: http://192.168.137.127:9000
aliyun:
 accessKeyId: AAAAAAAAAAAAA
 secret: BBBBBBBBBBBB
#aliyun.scenes=porn,terrorism,ad,qrcode,live,logo
 scenes: terrorism
feign:
  # 开启feign对hystrix熔断降级的支持
  hystrix:
    enabled: true
  # 修改调用超时时间
  client:
    config:
      default:
        connectTimeout: 2000
        readTimeout: 2000
```

leadnews-wemedia-gateway

```
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true
        cors-configurations:
          '[/**]': # 匹配所有请求
            allowedOrigins: "*" #跨域处理 允许所有的域
            allowedMethods: # 支持的方法
              - GET
              - POST
              - PUT
              - DELETE
      routes:
        # 平台管理
        - id: wemedia
          uri: lb://leadnews-wemedia
          predicates:
            - Path=/wemedia/**
          filters:
            - StripPrefix= 1
        #新的前端页面有坑，路径改了并且用的是admin路径，需要把admin路径也设置为当前服务
        - id: admin
          uri: lb://leadnews-wemedia
          predicates:
            - Path=/admin/**
          filters:
            - StripPrefix= 1

```

leadnews-schedule

```
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/leadnews_schedule?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false
    username: root
    password: 123456
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
# 设置Mapper接口所对应的XML文件位置，如果你在Mapper接口中有自定义方法，需要进行该配置
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.schedule.pojos
```

leadnews-search

```
spring:
  data:
    mongodb:
      host: 192.168.137.127
      port: 27017
      database: leadnews-history
  autoconfigure:
    #忽略自动配置
    exclude: org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
  kafka:
    bootstrap-servers: 192.168.137.127:9092
    consumer:
      group-id: ${spring.application.name}
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
elasticsearch:
  host: 192.168.137.127
  port: 9200

```

leadnews-admin-gateway

```
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true
        cors-configurations:
          '[/**]': # 匹配所有请求
            allowedOrigins: "*" #跨域处理 允许所有的域
            allowedMethods: # 支持的方法
              - GET
              - POST
              - PUT
              - DELETE
      routes:
        - id: admin
          uri: lb://leadnews-admin
          predicates:
            - Path=/admin/**
          filters:
            - StripPrefix= 1
        
```

leadnews-admin

```
spring:
  data:
    mongodb:
      host: 192.168.137.127
      port: 27017
      database: leadnews-comment
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/leadnews_admin?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false
    username: root
    password: 123456
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.media.pojos
feign:
  # 开启feign对hystrix熔断降级的支持
  hystrix:
    enabled: true
  # 修改调用超时时间
  client:
    config:
      default:
        connectTimeout: 2000
        readTimeout: 2000
minio:
  accessKey: minio
  secretKey: minio123
  bucket: leadnews
  endpoint: http://192.168.137.127:9000
  readPath: http://192.168.137.127:9000
```

leadnews-behavior

```
spring:
  application:
    name: leadnews-behavior
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/leadnews_article?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false
    username: root
    password: 123456
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
  kafka:
    bootstrap-servers: 192.168.137.127:9092
    producer:
      retries: 10
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.media.pojos
```

leadnews-behavior-gateway

```
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true
        cors-configurations:
          '[/**]': # 匹配所有请求
            allowedOrigins: "*" #跨域处理 允许所有的域
            allowedMethods: # 支持的方法
              - GET
              - POST
              - PUT
              - DELETE
      routes:
        - id: admin
          uri: lb://leadnews-admin
          predicates:
            - Path=/admin/**
          filters:
            - StripPrefix= 1
        
```

leadnews-comment

```
spring:
  kafka:
    bootstrap-servers: 192.168.137.127:9092
    producer:
      retries: 10
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
  redis:
    host: 192.168.137.127
    #password: leadnews
    port: 6379
# 设置Mapper接口所对应的XML文件位置，如果你在Mapper接口中有自定义方法，需要进行该配置
mybatis-plus:
  mapper-locations: classpath*:mapper/*.xml
  # 设置别名包扫描路径，通过该属性可以给包中的类注册别名
  type-aliases-package: com.heima.model.commment.pojos
kafka:
  hosts: 192.168.137.127:9092
  group: ${spring.application.name}
```

其它部分大致都可以从黑马提供的资料包中找到