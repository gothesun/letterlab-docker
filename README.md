# letterlab-docker
letterlab在线文献管理系统，基于B/S架构 &amp; Spring Boot + Vue技术栈开发，可用于个人或中小型企业、学校、电商等进行文献管理、收藏。

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
    image: registry.cn-shenzhen.aliyuncs.com/gothesun/letterlab-app:1.0.0
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
    image: registry.cn-shenzhen.aliyuncs.com/gothesun/letterlab-web:1.0.0
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
