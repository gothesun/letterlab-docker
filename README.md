# letterlab-docker
letterlab在线文献管理系统，基于B/S架构 &amp; Spring Boot + Vue技术栈开发，可用于个人或中小型企业、学校、电商等进行电子书、论文的管理、收藏。

## 快速开始
推荐使用docker compose部署：
```java
version: "3.8"
services:

  elasticsearch-ik:
    container_name: elasticsearch-ik
    image: zmlccf/elasticsearch-ik:7.12.1
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - elasticsearch-ik:/usr/share/elasticsearch/data
    restart: always
    environment:
      discovery.type: single-node
      xpack.security.enabled: "true"
      ES_JAVA_OPTS: "-Xms2g -Xmx2g"
      ELASTIC_PASSWORD: admin123

  letterlab-app:
    container_name: letterlab-app
    image: gothesun/letterlab-app:1.0.0
    depends_on:
      elasticsearch-ik:
        condition: service_healthy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - letterlab-app:/opt/gothesun/apps/letterlab-app/data
    restart: always
    environment:
      WEB_DOMAIN: http://WEB_DOMAIN
      ELASTICSEARCH_URI: elasticsearch-ik:9200
      ELASTIC_PASSWORD: admin123

  letterlab-web:
    container_name: letterlab-web
    image: gothesun/letterlab-web:1.0.0
    depends_on:
      elasticsearch-ik:
        condition: service_healthy
    restart: always
    environment:
      APP_DOMAIN: http://APP_DOMAIN

  nginx:
    container_name: nginx
    image: nginx:1.21.0
    depends_on:
      letterlab-web:
        condition: service_healthy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - $PWD/nginx/nginx.conf:/etc/nginx/nginx.conf
    restart: always
    ports:
      - "80:80"

volumes:
  elasticsearch-ik:
  letterlab-app:
```
注意，需将上述代码中的`WEP_DOMAIN`和`APP_DOMAIN`替换为自己实际的域名（ip）。

启动服务仅需三步：
1. 下载仓库至服务器上：
``` bash
git clone https://github.com/gothesun/letterlab-docker.git
```
2. 进入letterlab-docker目录，修改`WEP_DOMAIN`和`APP_DOMAIN`为实际域名
3. 启动docker容器：
``` bash
docker-compose -d up
```
待容器都正常启动，即可通过域名访问letterlab系统。

## 环境变量
### letterlab-app

| 变量名                   | 含义                                                     | 类型     | 默认值                |
| ------------------------ | -------------------------------------------------------- | -------- | --------------------- |
| ELASTICSEARCH_URI        | elasticsearch服务的地址，如localhost:9200                | 字符串   | 无                    |
| ELASTIC_PASSWORD         | elasticsearch服务的密码，如admin123                      | 字符串   | 无                    |
| WEB_DOMAIN               | letterlab-web前端服务的域名，如http://192.168.0.100:3000 | 字符串   | http://localhost:3000 |
| ADMIN_NICKNAME           | 超级管理员用户的昵称，如admin                            | 字符串   | admin                 |
| ADMIN_PASSWORD           | 超级管理员用户的密码，如admin123                         | 字符串   | admin123              |
| ADMIN_EMAIL              | 超级管理员用户的邮箱，如admin@example.com                | 字符串   | admin@example.com     |
| FLOW_CONTROL_PERMITS     | 流控阈值，即每秒允许对后端服务的请求数                   | 整型数字 | 10000                 |
| ACCESS_FREQUENCY_PERMITS | 接口访问频率阈值，即每个用户对每个接口每秒允许的访问次数 | 整型数字 | 10                    |

### letterlab-web


| 变量名     | 含义                                                     | 类型   | 默认值                    |
| ---------- | -------------------------------------------------------- | ------ | ------------------------- |
| APP_DOMAIN | letterlab-app后端服务的域名，如http://192.168.0.100:8080 | 字符串 | letterlab-web前端服务域名 |

### elasticsearch-ik
elasticsearch-ik镜像是集成了ik-analysis分词器插件的elasticsearch镜像，用于中文分词，因此该镜像的环境变量和各项参数与elasticsearch基础镜像保持一致。

## 高级配置
若环境变量无法满足实际需求，可将letterlab-app镜像中$APP_HOME/resources目录下的application.properties文件拷贝到宿主机，修改配置项后通过docker volume挂载的形式重新启动letterlab-app容器。

## 代码托管
- [github](https://github.com/gothesun/letterlab-docker)
- [gitee](https://gitee.com/gothesun/letterlab-docker)

## 交流反馈
欢迎提交issue反馈问题，亦可通过QQ联系作者交流：2461836917。

## 版权声明
作者保留letterlab文献管理系统的著作权，读者可下载letterlab做学习、研究之用，不得用于商业目的。作者不对因不当使用letterlab系统产生的任何事故、商业纠纷等负有责任。
