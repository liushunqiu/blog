---
title: 图片服务器Fastdfs配置nginx缩略图
date: 2018-04-29 22:46:25
tags:
	- nginx
	- Fastdfs
---
1.下载并安装FastDFS依赖包libfastcommon
```
wget https://codeload.github.com/happyfish100/libfastcommon/zip/master
unzip master
cd libfastcommon-master/
./make.sh
./make.sh install
```
2.下载并安装FastDFS 
```
wget https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz
tar xf V5.11.tar.gz
cd fastdfs-5.11
 ./make.sh && ./make.sh install
```
3.配置跟踪服务器（tracker server）
```
1）拷贝tracker server和client端样例配置文件并重命名
cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf
2）编辑tracker server配置文件tracker.conf，需要修改内容如下：
disabled=false（默认为false，表示是否无效）
port=22122（默认为22122）
base_path=/home/fastdfs/tracker
3）编辑client端的配置文件client.conf，需要修改内容如下
 base_path=/home/fastdfs/tracker
 tracker_server=192.168.1.147:22122
4）创建tracker server数据目录
mkdir -p /home/fastdfs/tracker
5）测试启动tracker server，启动成功会自动在/home/fastdfs/tracker目录新建data和logs目录
/etc/init.d/fdfs_trackerd start
6）关闭tracker server
```
4、配置存储服务器（storage server）
```
1）拷贝storage server样例配置文件并重命名
cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
2）编辑storage server配置文件storage.conf，需要修改内容如下：
disabled=false（默认为false，表示是否无效）
port=23000（默认为23000）
base_path=/home/fastdfs/storage
tracker_server=192.168.1.147:22122
store_path0=/home/fastdfs/storage
http.server_port=8888（默认为8888，nginx中配置的监听端口那之一致）
3）创建storage server数据目录
mkdir -p /home/fastdfs/storage
4）测试启动storage server，启动成功会自动在/data/fastdfs/tracker目录新建data和logs目录（启动storage server的前提是tracker server必须事先已启动）
/etc/init.d/fdfs_storaged start
```
5、文件上传测试
```
执行如下上传命令： fdfs_upload_file /etc/fdfs/client.conf  login.jpg
group1/M00/00/00/wKgBk1nd1ImAJ7MiAAJwztHvyoo346.jpg
```
5.存储服务器（storage server）安装并配置nginx
```
1).下载fastdfs-nginx-module模块	
2）拷贝fastdfs-nginx-module模块中配置文件到/etc/fdfs目录中并编辑
cp /root/fastdfs-nginx-module-master/src/mod_fastdfs.conf /etc/fdfs/
vim /etc/fdfs/mod_fastdfs.conf
修改内容如下：
connect_timeout=10
base_path=/tmp（默认为/tmp）
tracker_server=192.168.1.147:22122
storage_server_port=23000（默认配置为23000）
url_have_group_name = true
store_path0=/home/fastdfs/storage
group_name=group1（默认配置为group1）
3）安装nginx依赖库
yum install -y pcre-devel zlib-devel  gd-devel
4）下载并安装nginx
wget https://nginx.org/download/nginx-1.12.1.tar.gz
tar -zxf nginx-1.12.1.tar.gz
cd nginx-1.12.1
./configure --prefix=/application/nginx/ --add-module=../fastdfs-nginx-module-master/src/ --with-http_image_filter_module
make && make install
5）拷贝FastDFS中的部分配置文件到/etc/fdfs目录中
cp  /root/fastdfs-5.11/conf/http.conf  /etc/fdfs/
cp  /root/fastdfs-5.11/conf/mime.types  /etc/fdfs/
6）配置nginx，如下所示：vim /application/nginx/conf/nginx.conf
 user  root;    
        worker_processes  1;
 events {
       worker_connections  1024;
 }
   	http {
        include       mime.types;
        default_type  application/octet-stream;
        sendfile        on;
        keepalive_timeout  65;
        server {
            listen       8888;
            server_name  localhost;
            
     location ~/group[0-9]/M[0-1][0-9]/.*,([0-9]+)x([0-9]+)\.(jpg|png|gif)$ {
            alias /home/fastdfs/storage/data;
            ngx_fastdfs_module;
            set $img_width2 $1;
            set $img_height2 $2;
            rewrite ^(.*),[0-9]+x[0-9]+\.(jpg|png|gif)$ $1.$2  break;
            image_filter resize  $img_width2   $img_height2;
     }
     location ~/group[0-9]/M[0-1][0-9]/(.*){
            alias /home/fastdfs/storage/data;
            ngx_fastdfs_module;
     }
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
            	root   html;
            }
        }
  }
 ```
7）拷贝nginx服务到/etc/init.d/目录下并启动
```
cp /application/nginx/sbin/nginx /etc/init.d/
/etc/init.d/nginx
``` 
8).通过浏览器访问