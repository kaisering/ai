# 基础镜像选择，基于OpenJDK 8，因为Nacos Server依赖Java环境
FROM openjdk:8-jdk-alpine

# 设置工作目录，在镜像内创建/app目录存放Nacos Server相关文件
WORKDIR /app

# 复制本地解压后的Nacos Server文件到镜像内的/app目录
COPY nacos /app/nacos

# 暴露Nacos Server常用端口，8848用于HTTP访问，9848用于集群间通信（可按需调整）
EXPOSE 8848
EXPOSE 9848

# 设置环境变量，用于指定启动模式，默认为单体模式，可通过外部传入参数修改
ENV MODE="standalone"

# 设置入口点脚本，用于处理启动逻辑，这里先创建一个临时的启动脚本
RUN echo '#!/bin/sh' > /docker-entrypoint.sh && \
    echo 'if [ "$MODE" = "cluster" ]; then' >> /docker-entrypoint.sh && \
    echo '    sh /app/nacos/bin/startup.sh' >> /docker-entrypoint.sh && \
    echo 'else' >> /docker-entrypoint.sh && \
    echo '    sh /app/nacos/bin/startup.sh -m standalone' >> /docker-entrypoint.sh && \
    echo 'fi' >> /docker-entrypoint.sh && \
    chmod +x /docker-entrypoint.sh

# 指定入口点，容器启动时执行该脚本
ENTRYPOINT ["/docker-entrypoint.sh"]
