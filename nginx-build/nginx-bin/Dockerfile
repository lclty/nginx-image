# 使用最轻量的 Alpine Linux
FROM alpine:latest

# 1. 安装 NGINX 运行时必须的动态库
RUN apk add --no-cache \
    pcre2 \
    zlib \
    libgcc \
    libxml2 \
    libxslt \
    gd \
    geoip \
    perl \
    libedit \
    tzdata

# 2. 创建用户并建立需要程序运行时写入的目录
RUN addgroup -S nginx && adduser -S -G nginx -H -D nginx && \
    mkdir -p /data/logs \
             /data/configs \
             /data/sites && \
    chown -R nginx:nginx /data

# 3. 将宿主机提取出的完整文件夹拷贝进去，并赋予权限
COPY --chown=nginx:nginx ./nginx/ /nginx/
COPY --chown=nginx:nginx ./config/ /config/

# 4. 确保临时目录存在且权限正确
RUN mkdir -p /nginx/run \
             /nginx/temp/client_body \
             /nginx/temp/proxy \
             /nginx/temp/fastcgi \
             /nginx/temp/uwsgi \
             /nginx/temp/scgi && \
    chown -R nginx:nginx /nginx

# 5. 暴露端口
EXPOSE 80 443 443/udp

# 6. 启动命令
STOPSIGNAL SIGQUIT
CMD ["/nginx/nginx", "-g", "daemon off;"]
