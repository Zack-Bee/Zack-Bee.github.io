---
title: 使用ngxin-rtmp搭建直播服务
date: 2018-10-02 14:31:46
tags: 教程 直播 hdtv
categories: 教程
---

### 环境:
os: Debian(nginx与nginx-rtmp模块支持的系统都行, 虽然有windows的版本, 但是最好在windows环境下使用wsl虚拟机)

nginx版本: 1.15.3(nginx与nginx-rtmp都是选的比较新的, 老旧版本编译安装很可能出错)

nginx-rtmp版本: 1.2.1

gcc版本: 8.2.0

### 编译安装
假定nginx目录在/usr/local/src/nginx, 
nginx-rtmp目录在user/local/src/nginx-rtmp, 
目标安装目录在/user/local/ngxin, 则使用的命令参数如下
```
cd /usr/local/src/nginx
./configure --prefix=/usr/local/nginx --with-ipv6 --add-module=/usr/local/src/nginx-rtmp --without-http_gzip_module --without-http_rewrite_module
make
make install
// 在`make`与`make install`的过程中, 很容易出错, 通常是编译错误, 需要在switch中的case语句前加上/*fall thru*/
//详情到如下地址查看https://developers.redhat.com/blog/2017/03/10/wimplicit-fallthrough-in-gcc-7/
// 安装时也会出现缺少open-ssl库的问题, 到stackoverflow上搜索即可.
```

### 更改ngxin-conf
使用如下将nginx.conf修改成如下内容
{% codeblock nginx.conf配置 lang:%}
user www-data;
# worker_processes auto;
# pid /run/nginx.pid;
# include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 1024;
}

rtmp {

    server {

        listen 1935;

        chunk_size 4000;

        # 用于直播推流的认证
        application live {
            live on;
            publish_notify on;
            on_publish http://localhost:3000/authorize;
            hls on;
            hls_path /usr/local/live/hls;
            on_publish_done http://localhost:3000/liveDone;
            hls_playlist_length 15s;
        }

        application vod {
            play /var/flvs;
        }
    }
}

# HTTP can be used for accessing RTMP stats
http {

    server {

        listen      8080;

        location /stat.xsl {
            # XML stylesheet to view RTMP stats.
            # Copy stat.xsl wherever you want
            # and put the full directory path here
            root /usr/local/nginx/sbin/;
        }

        #在服务器生成hls文件
        location /hls {
            # Serve HLS fragments
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            root /usr/local/hdtv;
            add_header Cache-Control no-cache;
        }

        location /dash {
            # Serve DASH fragments
            root /tmp;
            add_header Cache-Control no-cache;
        }
    }
}
{% endcodeblock%}

### 直播推流
将nginx.conf中的on_publish那一行删掉, 启动nginx即可进行推流, 在配置文件中, listen后面为1935, application后面为live, 那么推流地址应该是rtmp://localhost:1935/live/:name, name为你自定义的参数, 在rtmp推流中, 该参数是必须的.

如果想要控制直播推流, 比如加上推流认证, 那么在nginx.conf中的on_publish后面填上你用来处理的推流的url, 我的conf设置里on_publish后面为http://localhost:3000/authorize, 当产生rtmp推流请求时, nginx-rtmp模块会向该url发送Post请求(如果希望nginx-rtmp向该url发送Get请求则需要在nginx.conf中配置, 请自行查阅), 如果该url的处理程序返回200, 则nginx-rtmp允许推流, 返回4开头或者5开头分状态码则会终止此次推流.

假如推流地址为rtmp://localhost:1935/live/test?pass=123456, 那么控制直播推流收到的请求会有{name: "test", pass: "123456"}这样的数据, 当然还会有其他关于推流信息的数据, 请自行打印查看.

向rtmp://localhost:1935/live/test?pass=123456, 播放地址为rtmp://localhost:1935/live/test, 使用rtmp协议直接播放的优点是延迟低, 占用内存小, 缺点是在浏览器上兼容性不好, 只能使用flash播放. 在ngxin.conf中的`hls on;``hls_path /usr/local/live/hls;`这两行配置, 可以在hls_path指定的目录下生成用于hls协议的.ts以及.m3u8文件, 使用hls协议的优点是兼容性较好, 在safari中可以直接播放, 其他浏览器可以使用http获取视频流, 然后进行处理后喂给video元素, 基本上满足各个平台的浏览器播放. 缺点也很明显, 直播延迟高, 生成.ts以及.m3u8占用内存.

### 其他
你可以使用[hdtv](https://github.com/Zack-Bee/hdtv)以及[hdtv-admin](https://github.com/Zack-Bee/hdtv-admin)来对视频进行播放以及推流认证, 账户管理等功能. hdtv将来会考虑自己实现flv的直播来降低直播产生的延迟, 同时加入弹幕等功能.