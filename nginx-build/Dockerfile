# 使用 Alpine 作为编译底座（最轻量的构建环境）
FROM alpine:latest AS builder

# 安装所有必要的编译工具和开发库
RUN apk add --no-cache \
    gcc \
    libc-dev \
    make \
    linux-headers \
    perl-dev \
    libedit-dev \
    gd-dev \
    libxml2-dev \
    libxslt-dev \
    geoip-dev \
    bash \
    g++

# 设置工作目录
WORKDIR /source

# 拷贝准备好的四个源码包并解压
COPY nginx-1.28.3.tar.gz .
COPY openssl-3.6.1.tar.gz .
COPY pcre2-10.47.tar.gz .
COPY zlib-1.3.2.tar.gz .
RUN tar -zxf nginx-1.28.3.tar.gz && \
    tar -zxf openssl-3.6.1.tar.gz && \
    tar -zxf pcre2-10.47.tar.gz && \
    tar -zxf zlib-1.3.2.tar.gz
    
# 进入 NGINX 目录
WORKDIR /source/nginx-1.28.3

# ==========================================
# 核心路径重新规划
# /nginx : 可执行文件、动态模块、运行时的临时文件和 PID
# /config: NGINX 的主配置、MIME 类型等静态模板
# /data  : 宿主机挂载点
# ==========================================
RUN ./configure \
    --prefix=/nginx \
    --sbin-path=/nginx/nginx \
    --modules-path=/nginx/modules \
    --conf-path=/config/nginx.conf \
    --error-log-path=/data/logs/error.log \
    --http-log-path=/data/logs/access.log \
    --pid-path=/nginx/run/nginx.pid \
    --lock-path=/nginx/run/nginx.lock \
    --http-client-body-temp-path=/nginx/temp/client_body \
    --http-proxy-temp-path=/nginx/temp/proxy \
    --http-fastcgi-temp-path=/nginx/temp/fastcgi \
    --http-uwsgi-temp-path=/nginx/temp/uwsgi \
    --http-scgi-temp-path=/nginx/temp/scgi \
    --user=nginx \
    --group=nginx \
    --build="build_by_lclty_March2026ver" \
    --with-select_module \
    --with-poll_module \
    --with-threads \
    --with-file-aio \
    --with-http_ssl_module \
    --with-http_v2_module \
    --with-http_v3_module \
    --with-http_realip_module \
    --with-http_addition_module \
    --with-http_xslt_module \
    --with-http_image_filter_module \
    --with-http_geoip_module \
    --with-http_sub_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_mp4_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_auth_request_module \
    --with-http_random_index_module \
    --with-http_secure_link_module \
    --with-http_degradation_module \
    --with-http_slice_module \
    --with-http_stub_status_module \
    --with-http_perl_module \
    --with-mail \
    --with-mail_ssl_module \
    --with-stream \
    --with-stream_ssl_module \
    --with-stream_realip_module \
    --with-stream_geoip_module \
    --with-stream_ssl_preread_module \
    --with-compat \
    --with-pcre-jit \
    --with-pcre=/source/pcre2-10.47 \
    --with-zlib=/source/zlib-1.3.2 \
    --with-openssl=/source/openssl-3.6.1 \
    --with-openssl-opt="no-shared" \
    --with-cc-opt="-O3 -flto" \
    --with-ld-opt="-Wl,-z,relro,-z,now" \
    --with-perl_modules_path=/nginx/perl_lib

# 编译并执行安装（把所有生成的文件、模块、配置铺到指定的 /nginx 和 /config 目录中）
RUN make -j$(nproc) && make install

# 编译完成后默认进入 shell 方便提取文件
CMD ["sh"]
