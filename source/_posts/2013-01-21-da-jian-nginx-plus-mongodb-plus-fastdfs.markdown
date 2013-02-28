---
layout: post
title: "搭建nginx+mongodb+fastdfs"
date: 2013-01-21 11:11
comments: true
categories: 
---
## 1.	nginx
此次所需插件：
> 1.   nginx-upstream-fair
> 2.   nginx_upload_module
> 3.   ngx_http_auth_request_module
> 4.   fastdfs-nginx-module

<!-- more-->
1.	nginx-upstream-fair

“公平的”Nginx 负载均衡模块，增强了Nginx 提供的round-robin 负载均衡算法，可以跟踪后端服务器的负载来分发请求。

下载：
	git clone git://github.com/gnosek/nginx-upstream-fair.git
配置范例：

	upstream mongrel {
	    fair;
	    server 127.0.0.1:5000;
	    server 127.0.0.1:5001;
	    server 127.0.0.1:5002;
	}
	
2.	nginx_upload_module

nginx upload module模块通过nginx服务器来接受用户上传的文件，它自动分析客户端的上传请求，将上传的文件保存到 upload_store 所指向的目录位置。
然后这些文件信息将被从原始的请求中剔除，重新组装好上传参数后转交到后端由 upload_pass 指定的location去处理，这样后端就可以任意处理上传的文件。
每一个上传的 file 字段值将可以由upload_set_form_field 指定的值替换。
上传的文件可以通过$upload_tmp_path 变量访问到。
上传的文件经过处理以后，由 upload_cleanup 指定的条件控制删除清理。

下载:
	git clone git://github.com/vkholodkov/nginx-upload-module.git

3.	ngx_http_auth_request_module

这个模块可以实现基于服务器内部子请求的返回结果来控制用户鉴权。比如子请求返回4xx则提示说没有权限，如果是2xx则会返回资源给客户端，返回401错误则把子请求的鉴权头透传给客户端。这样通过服务器内部逻辑就可以控制鉴权，省去不少业务逻辑层的脚本代码.

下载:
	git clone git://github.com/PiotrSikora/ngx_http_auth_request_module.git

Usage:

    location /private/ {
        auth_request /auth;
        ...
    }

    location = /auth {
        proxy_pass ...
        proxy_pass_request_body off;
        proxy_set_header Content-Length "";
        proxy_set_header X-Original-URI $request_uri;
    }

4.	fastdfs-nginx-module

下载:
	wget http://fastdfs-nginx-module.googlecode.com/files/fastdfs-nginx-module_v1.14.tar.gz
	tar xzvf fastdfs-nginx-module_v1.14.tar.gz

最后是nginx
	wget http://nginx.org/download/nginx-1.2.1.tar.gz
	tar xzvf nginx-1.2.1.tar.gz
	cd nginx-1.2.1
	./configure --with-http_ssl_module --with-http_flv_module --with-http_gzip_static_module --with-mail --with-mail_ssl_module --with-poll_module --with-http_stub_status_module --with-http_perl_module --add-module={nginx_plugin_path}/nginx-upstream-fair --add-module={nginx_plugin_path}/nginx-upload-module --add-module={nginx_plugin_path}/ngx_http_auth_request_module --add-module={nginx_plugin_path}/fastdfs-nginx-module/src/
	make && make install
在make的时候会报错,没有fdfs_define.h,那是因为还没有安装fastdfs.先安装fastdfs,回头再来编译nginx

## 2.	fastdfs

FastDFS是一款类Google FS的开源分布式文件系统，它用纯C语言实现，支持Linux、FreeBSD、AIX等UNIX系统。它只能通过专有API对文件进行存取访问，不支持POSIX接口方式，不能mount使用。准确地讲，Google FS以及FastDFS、mogileFS、HDFS、TFS等类Google FS都不是系统级的分布式文件系统，而是应用级的分布式文件存储服务。

依赖libevent:
	wget http://cloud.github.com/downloads/libevent/libevent/libevent-1.4.14b-stable.tar.gz
	cd libevent-1.4.14b
	./configure
	make && make install
安装Fastdfs:
	wget http://fastdfs.googlecode.com/files/FastDFS_v4.05.tar.gz
	tar xzvf FastDFS_v4.05.tar.gz
	cd FastDFS
	./make && ./make install
配置Fastdfs:
	mkidr -p /home/data/fastdfs/db
	mkidr -p /home/data/fastdfs/log
	vi /etc/fdfs/tracker.conf 
	修改base_path=/home/data/fastdfs
	vi /etc/fdfs/storage.conf
	修改base_path=/home/data/fastdfs
	修改tracker_server=192.168.1.100:22122
以上配置根据自己的电脑进行配置
启动:
	fdfs_trackerd /etc/fdfs/tracker.conf 
	fdfs_storaged /etc/fdfs/storage.conf
监控:
	fdfs_monitor /etc/fdfs/client.conf
测试:
	fdfs_upload_file /etc/fdfs/client.conf test.tx
	返回:group1/M00/00/00/wKgBZFELUiCAMJzqAAAABmpcy6c9964.tx
	fdfs_download_file /etc/fdfs/client.conf group1/M00/00/00/wKgBZFELUiCAMJzqAAAABmpcy6c9964.tx test.tx

重新回到之前编译nginx的地方.
	./configure --with-http_ssl_module --with-http_flv_module --with-http_gzip_static_module --with-mail --with-mail_ssl_module --with-poll_module --with-http_stub_status_module --with-http_perl_module --add-module={nginx_plugin_path}/nginx-upstream-fair --add-module={nginx_plugin_path}/nginx-upload-module --add-module={nginx_plugin_path}/ngx_http_auth_request_module --add-module={nginx_plugin_path}/fastdfs-nginx-module/src/

	make && make install
耐心等待,应该没有什么问题.
配置nginx.conf 
	vi /etc/nginx/nginx.conf
增加:
	location ~ ^/group1/M00/ {
                root /home/data/fastdfs/data;
                expires 4d;
                ngx_fastdfs_module;
        }




	