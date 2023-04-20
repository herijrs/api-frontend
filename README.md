------

### 后端技术选型

- Java Spring Boot
- MySQL数据库
- MyBatis-Plus 及 MyBatis X 自动生成
- API签名认证
- Spring Boot Starter(SDK开发)
- Dubbo分布式（RPC、Nacos)
- Swagger + Knief4j 接口文档生成
- Spring Cloud Gateway 微服务网关
- Hutool、Apache Common Utils、Gson等工具库



### 前端技术选型

- React18

- Ant Design Pro 5.x 脚手架

- Ant Design & Procomponents组件库

- Umi4前端框架

- OpenAPI 前端代码生成

  

------

### 部署前端项目
```xml
# 修改requestConfig.ts文件中的配置  baseURL: 'http://公网ip:7529'

# config/config.ts中添加 exportStatic: {}（静态化生成页面）

# 打包 build

# 设置nginx配置

location / {
    root   /root/services/api-frontend;
    index  index.html index.htm;
    # try_files 表示检查文件是否存在，返回第一个找到的文件，这里设置是index.html内部重定向。
    try_files $uri $uri/index.html /index.html;
}
    
location /api {
    rewrite ^/api/(.*) /$1 break;
    # 后台服务地址
    proxy_pass http://101.43.174.74/api;
    proxy_set_header   X-Forwarded-Proto $scheme;
    proxy_set_header   Host              $http_host;
    proxy_set_header   X-Real-IP         $remote_addr;
}
```


### 部署后端项目

```bash
# docker部署Nacos
# 下载nacos镜像
 docker pull nacos/nacos-server

 # 以单机模式启动nacos
 docker run --name nacos \
-e MODE=standalone \
-p 8848:8848 -d \
-p 9848:9848 \
--restart always nacos/nacos-server:latest 

docker run -d -p 8848:8848 \
--name nacos \
--env MODE=standalone \
nacos/nacos-server:1.4.1

# 修改dubbo配置host地址为服务器内网地址，向nacos注册服务
# 配置为
dubbo:
  registry:
    address: nacos://服务区内网地址:8848

# 修改模拟接口地址为服务器地址
private static final String INTERFACE_HOST = "http://服务器地址外网:8123";

# 打包构建，跳过测试
mvn package -DskipTests

# 创建自启脚本jar_auto.sh
nohup java -jar api-interface-0.0.1-SNAPSHOT.jar &
nohup java -jar api-backend-0.0.1-SNAPSHOT.jar &
nohup java -jar api-gateway-0.0.1-SNAPSHOT.jar &

```

