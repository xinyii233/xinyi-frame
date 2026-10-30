# 基础镜像
FROM openjdk:8-jre-slim

# 作者
LABEL org.opencontainers.image.authors="心意 (xinyi)"

# 配置
ENV PARAMS=""

# 时区
ENV TZ=PRC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 添加应用
COPY target/xinyi-frame-app.jar /xinyi-frame-app.jar

ENTRYPOINT ["sh","-c","exec java $JAVA_OPTS -jar /xinyi-frame-app.jar $PARAMS"]
