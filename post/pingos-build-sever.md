---
title: "搭建流媒体服务器 PingOS 平台搭建"
date: 2020-04-03T23:19:45+08:00
keywords: "pingos,ffmpeg,hls"
comments: true
tags: ["pingos","ffmpeg","hls"]
categories: ["debuginn"]
image: "https://static.debuginn.com/202303121930279.jpg"
---


近期由于工作原因需要更换公司原有 RTMP 协议推流，由于 Flash 插件今年年底就淘汰使用，并且一直在寻找一种并发好、延时低、同时便于回放功能的应用，在网上找到了基于Nginx + FFmpeg 推流的解决方案，可以实现 HLS 协议推流，看项目介绍可以实现 HLS+ 协议，这个工具安装比较便捷。

首先介绍一下这个开源的项目，欢迎给他们 star，谢谢。

[https://github.com/pingostack/pingos](https://github.com/pingostack/pingos)

官网地址：[https://pingos.io/](https://pingos.io/)

## 安装

项目文档：[https://pingos.io/docs/zh/quick-start](https://pingos.io/docs/zh/quick-start)

在官方网站的项目文档中讲解的不是很清晰，特别是针对新手来说是有一定的难度，我这里使用的是 Linux CentOS 7.4 64位环境，需要提前安装 Git 应用，这个就不详细讲解了，接下来讲一下如何安装：

### 1 下载源码

```shell
git clone https://github.com/pingostack/pingos.git
```

### 2 快速安装

```shell
cd pingos
./release.sh -i
```

### 3 启动服务

```shell
cd /usr/local/pingos/
./sbin/nginx
```

## 配置

一般情况下，安装完毕 PingOS 后就可以使用了，通过配置文件可以看到 nginx 占用端口为80，rtmp 端口占用为1935 。

但是在实际情况下，80 端口一般是使用于 HTTP 等服务，所以说尽量将服务端口设置为非 80 端口，由于使用了阿里云，可以关闭防火墙，同时配置安全组策略将 8080 入端口设置为允许状态。

下面是修改好的配置文件，位置为：`/usr/local/pingos/conf/nginx.conf`：

```shell
user  root;
daemon on;
master_process on;
worker_processes  1;
#worker_rlimit 4g;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
error_log  logs/error.log  info;

worker_rlimit_nofile 102400;
worker_rlimit_core   2G;
working_directory    /tmp;

#pid        logs/nginx.pid;

events {
#    use epoll;
    worker_connections  1024;
    multi_listen unix:/tmp/http 8080;
    multi_listen unix:/tmp/rtmp 1935;
}

stream_zone buckets=1024 streams=4096;

rtmp {
    log_format log_bandwidth '{"app":"$app","name":"$name","bitrate":$bitrate,"args":"$args","timestamp":$ntp,"ts":"$time_local","type":"$command","remote_addr":"$remote_addr","domain":"$domain"}';
    access_log logs/bandwidth.log log_bandwidth trunc=60s;

    server {
        listen 1935;
        serverid 000;
        out_queue 2048;
        server_name localhost;
   
        application live {
            rtmp_auto_pull on;
            rtmp_auto_pull_port unix:/tmp/rtmp;

#            live_record on;
#            live_record_path /tmp/record;

#            recorder r1{
#                record all;
#                record_path /tmp/record;
#            }

#            exec_publish bash -c "ffmepg -i rtmp://127.0.0.1/live/$name -c copy /tmp/mp4/$name-$starttime.mp4";

            live on;
            hls on;
            hls_path /tmp/hls;
            hls_fragment 4000ms;
#            hls_max_fragment 6000ms;
            hls_playlist_length 12000ms;

            hls2memory on;
            mpegts_cache_time 20s;

            hls2_fragment 1300ms;
            hls2_max_fragment 1600ms;
            hls2_playlist_length 3900ms;

            wait_key on;
            wait_video on;
            cache_time 3s;
            low_latency off;
            fix_timestamp 2s;
# h265 codecid, default 12
            hevc_codecid  12;
        }
    }
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_X-Forwarded-For" "$http_X-Real-IP" "$host"';


    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #reset_server_name www.test1.com www.test2.com;
    #gzip  on;
    server {
         listen 8080;
        location /rtmp_stat {
            rtmp_stat all;
            rtmp_stat_stylesheet /stat.xsl;
        }

        location /xstat {
            rtmp_stat all;
        }

        location /sys_stat {
            sys_stat;
        }

        location /control {
            rtmp_control all;
        }

        location /live {
            flv_live 1935;
            add_header 'Access-Control-Allow-Origin' '*';
            add_header Cache-Control no-cache;
        }

        location /ts {
            ts_live 1935 app=live;
        }

        location /hls {
            # Serve HLS fragments
             types {
                 application/vnd.apple.mpegurl m3u8;
                 video/mp2t ts;
             }
             root /tmp;
             add_header Cache-Control no-cache;
             add_header 'Access-Control-Allow-Origin' '*';
        }

        location /hls2 {
            hls2_live 1935 app=live;
            add_header 'Access-Control-Allow-Origin' '*';
            add_header Cache-Control no-cache;
        }

        location / {
             chunked_transfer_encoding on;
             root html/;
        }
    }
}
```

修改完配置之后需要进行 nginx 服务重新载入等操作。

## 命令

```shell
# 进入到 PingOS 应用目录，下面所有操作皆以此目录下进行
cd /usr/local/pingos/

# 开启 nginx 服务器
./sbin/nginx
# 检查 nginx 配置语法是否正确
./sbin/nginx -t
# 重新加载 nginx 配置
./sbin/nginx -s reload
# 停止 nginx 服务器
./sbin/nginx -s stop
```

## 推流

配置好服务器，可以看一下流媒体服务器推流效果，这里我是用的是 OBS 推流应用，推流端使用的是 RTMP 协议，在播放端使用的是 hls+ 协议。

这里给大家提供两个官方推荐查看推流效果的地址，也是应用提供的 Web 页面：

- `http://ip地址:端口/h5player/flv`  无插件播放http-flv直播流
- `http://ip地址:端口/rtmp_stat`  查看当前服务器推流统计数据

![OBS配置](https://static.debuginn.com/202303122236851.png)

播放地址：`http://ip地址:端口/hls2/流名.m3u8`

## 参考

- [PingOS 项目参考](https://pingos.io/docs/zh/quick-start)
- [怎么搭建hls低延时直播（lowlatency hls）- 知乎](https://zhuanlan.zhihu.com/p/87225094)

最后，这是一个系列的文章，后续还有针对 PingOS 流媒体服务还有对应优化，敬请关注

