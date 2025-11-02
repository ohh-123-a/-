# Docker Compose 文件编写教程

## 1. 概述

Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。通过 `docker-compose.yml` 文件，您可以配置应用程序的服务、网络和卷等。本教程将详细介绍 `docker-compose.yml` 文件的编写方法，并提供 6 个复杂案例。

## 2. `docker-compose.yml` 文件结构

### 2.1 基本结构

```yaml
version: '3.8'  # 指定 Docker Compose 文件版本
services:       # 定义服务
  service1:     # 服务名称
    image: image_name  # 使用的镜像
    ports:
      - "8080:80"  # 端口映射
    environment:   # 环境变量
      - ENV_VAR=value
    volumes:       # 卷映射
      - ./local_dir:/container_dir
    networks:     # 网络配置
      - network_name
  service2:
    image: another_image
    depends_on:   # 依赖关系
      - service1

networks:         # 定义网络
  network_name:
    driver: bridge

volumes:          # 定义卷
  volume_name:
```

### 2.2 常用指令和参数说明

- **version**: 指定 Docker Compose 文件格式的版本。
- **services**: 定义应用程序的各个服务。
  - **image**: 指定服务使用的镜像。
  - **ports**: 端口映射，格式为 `HOST:CONTAINER`。
  - **environment**: 设置环境变量。
  - **volumes**: 挂载卷，格式为 `HOST_DIR:CONTAINER_DIR`。
  - **networks**: 指定服务使用的网络。
  - **depends_on**: 定义服务启动顺序。
- **networks**: 定义网络配置。
  - **driver**: 网络驱动，如 `bridge`、`overlay` 等。
- **volumes**: 定义卷配置。

## 3. 案例

### 案例 1: Web 应用与数据库

#### 案例介绍
这是一个典型的 Web 应用与数据库的组合。Web 应用使用 Nginx 作为反向代理，后端使用 Flask 应用，数据库使用 PostgreSQL。

#### 代码

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - app

  app:
    image: my_flask_app:latest
    environment:
      - DATABASE_URL=postgres://user:password@db:5432/mydb
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

#### 代码解释
- **web**: 使用 Nginx 作为反向代理，映射本地 80 端口到容器的 80 端口，并挂载自定义的 Nginx 配置文件。
- **app**: 使用自定义的 Flask 应用镜像，依赖 `db` 服务，并设置数据库连接字符串。
- **db**: 使用 PostgreSQL 镜像，设置数据库用户、密码和数据库名称，并将数据持久化到 `postgres_data` 卷。

### 案例 2: 多服务微服务架构

#### 案例介绍
这是一个包含多个微服务的架构，包括 API 网关、用户服务、订单服务和消息队列。

#### 代码

```yaml
version: '3.8'

services:
  api_gateway:
    image: my_api_gateway:latest
    ports:
      - "8080:8080"
    depends_on:
      - user_service
      - order_service

  user_service:
    image: my_user_service:latest
    environment:
      - MESSAGE_QUEUE_URL=amqp://rabbitmq:5672
    depends_on:
      - rabbitmq

  order_service:
    image: my_order_service:latest
    environment:
      - MESSAGE_QUEUE_URL=amqp://rabbitmq:5672
    depends_on:
      - rabbitmq

  rabbitmq:
    image: rabbitmq:3.8-management
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: password

networks:
  default:
    driver: bridge
```

#### 代码解释
- **api_gateway**: API 网关服务，依赖 `user_service` 和 `order_service`。
- **user_service**: 用户服务，依赖 RabbitMQ 消息队列。
- **order_service**: 订单服务，依赖 RabbitMQ 消息队列。
- **rabbitmq**: RabbitMQ 消息队列服务，暴露管理界面端口。

### 案例 3: 使用 Traefik 作为反向代理

#### 案例介绍
使用 Traefik 作为反向代理，自动发现并路由到多个 Web 服务。

#### 代码

```yaml
version: '3.8'

services:
  traefik:
    image: traefik:latest
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --api.insecure=true --providers.docker

  web1:
    image: nginx:latest
    labels:
      - "traefik.http.routers.web1.rule=Host(`web1.example.com`)"
    depends_on:
      - traefik

  web2:
    image: nginx:latest
    labels:
      - "traefik.http.routers.web2.rule=Host(`web2.example.com`)"
    depends_on:
      - traefik
```

#### 代码解释
- **traefik**: Traefik 反向代理服务，监听 80 端口并提供管理界面。
- **web1** 和 **web2**: 两个 Web 服务，通过 Traefik 的标签配置路由规则。

### 案例 4: 使用 Redis 缓存

#### 案例介绍
一个使用 Redis 作为缓存的 Web 应用。

#### 代码

```yaml
version: '3.8'

services:
  web:
    image: my_web_app:latest
    ports:
      - "8080:8080"
    environment:
      - REDIS_HOST=redis
    depends_on:
      - redis

  redis:
    image: redis:6.0
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  redis_data:
```

#### 代码解释
- **web**: Web 应用服务，依赖 Redis 服务。
- **redis**: Redis 缓存服务，将数据持久化到 `redis_data` 卷。

### 案例 5: 使用 Elasticsearch 和 Kibana

#### 案例介绍
一个使用 Elasticsearch 和 Kibana 的日志分析系统。

#### 代码

```yaml
version: '3.8'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"
    volumes:
      - esdata:/usr/share/elasticsearch/data

  kibana:
    image: docker.elastic.co/kibana/kibana:7.10.0
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch

volumes:
  esdata:
```

#### 代码解释
- **elasticsearch**: Elasticsearch 服务，暴露 9200 端口。
- **kibana**: Kibana 服务，依赖 Elasticsearch，暴露 5601 端口。

### 案例 6: 使用 Prometheus 和 Grafana 监控

#### 案例介绍
一个使用 Prometheus 和 Grafana 的监控系统。

#### 代码

```yaml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
    depends_on:
      - prometheus

volumes:
  grafana_data:
```

#### 代码解释
- **prometheus**: Prometheus 监控服务，挂载自定义配置文件。
- **grafana**: Grafana 可视化服务，依赖 Prometheus，将数据持久化到 `grafana_data` 卷。
