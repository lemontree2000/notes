# Nginx开启SSL及网站CDN加速 

SSL（Secure Socket Layer）安全套接层是Netscape公司率先采用的网络安全协议。它是在传输[通信协议](https://baike.baidu.com/item/通信协议/3351624)（TCP/IP）上实现的一种安全协议，采用[公开密钥](https://baike.baidu.com/item/公开密钥/7453570)技术。SSL广泛支持各种类型的网络，同时提供三种基本的安全服务，它们都使用公开密钥技术。

### HTTPS有什么优点

HTTPS 在HTTP 的基础下加入[SSL](https://baike.baidu.com/item/SSL/320778) 层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL。与以往HTTP协议相比，HTTPS更加注重安全，有一下特点：

* 内容加密：采用混合加密技术，中间者无法直接查看明文内容
* 验证身份：通过证书认证客户端访问的是自己的服务器
* 保护数据完整性：防止传输的内容被中间人冒充或者篡改

### HTTPS大势所趋

近年来HTTPS在互联网中已经广泛应用，随着浏览器厂商和对HTTPS的强制要求，HTTPS协议肯定是未来建站必备条件。在Chrome 中网站如果是http协议，会被标记成不安全，相对于https则标识成安全加锁，而且在chrome中开发web各种api都需要在生产中使用https协议，如： navigator.geolocation、webRtc...

### 背景

笔者的服务器已经买了有一年多，之前偶尔折腾了一下就吃灰了。近来正好任务不是多，所以一致在研究docker及服务器这块。在使用docker部署网站后想着把快过期的ssl 证书更新一下，因为之前接的ssl被我直接重装了服务器。故重新配置一遍，并记录一下以被日后好对知识进行复盘。

### 申请证书

我的域名和服务器都在腾讯云上面购买的，所以也选择在腾讯云上面申请SSL证书，因为腾讯云一个主域可以申请20个免费的证书，但是不支持泛域名且证书有效期为一年，不过已经够个人用户使用了。

地址：[腾讯云免费SSL证书](https://console.qcloud.com/ssl)

申请成功后，将证书下载到本地。证书文件夹内容

- `1_www.domain.com_bundle.crt` 证书文件
- `2_www.domain.com.key` 私钥文件

使用ftp工具WinSCP 将两个文件上次的nginx目录下的conf文件夹里面

```bash
# 以下是服务器的路径
/usr/local/nginx/conf
```

### 配置nginx.conf

先查看一下nginx是否安装ssl模块,以下说明nginx已经安装并开启了ssl模块

```bash
[root@edward]# /usr/local/nginx/sbin/nginx -V
nginx version: nginx/1.12.0
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) 
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --with-http_ssl_module

```

如果没有安装则需要重新编译安装以下ssl模块

```bash
[root@edward]# ./configure --with-http_stub_status_module --with-http_ssl_module --with-http_realip_module --with-http_gzip_static_module
[root@edward]# make
[root@edward]# make install
```

修改nginx.conf,写入以下配置

```bash
[root@edward]#vim /usr/local/nginx/sbin/nginx/conf/ngxin.conf

server {
     #SSL 访问端口号为 443
     listen 443; 
     #填写绑定证书的域名
     server_name www.domain.com; 
     #启用 SSL 功能
     ssl on;
     #证书文件名称
     ssl_certificate 1_www.domain.com_bundle.crt; 
     #私钥文件名称
     ssl_certificate_key 2_www.domain.com.key; 
     ssl_session_timeout 5m;
     #请按照以下协议配置
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
     #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
     ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
     ssl_prefer_server_ciphers on;
     location / {
        #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
         root /var/www/www.domain.com; 
         index  index.html index.htm;
     }
 }
```

保存退出，执行nginx -t，提示success，则执行nginx -s reload

### 网站CDN加速

因为购买的服务器是最低配置1m的带宽，放置前端项目在加载的时候还是很慢的，因为最大速度只能达到150kb/s，而打包出来的app.js文件都快1m了，正好腾讯云提供了每月10gb的免费cdn流量,首次开通还赠送6个月每月50gb的流量。

#### 配置CDN

cdn配置不需要对服务器进行相应的改造，直接在腾讯云控制台操作即可。

配置步骤： 添加cdn域名->添加cdn缓存配置->域名进行cname解析

配置完成后及可以通过配置的域名进行访问的时候就会又加速的效果，测试了一下，没有cdn加速访问我的网站，一些js文件要加载7、8秒，cdn加速后整个网站两秒内就能打开，所以效果还是很明显的。

#### 关于cdn的问题

我cdn配置了www主域名，但是我在stacksweet.com网站的时候并没有加速效果，提了工单询问解决方案，技术人员建议我进行泛域名的cdn配置。由于泛域名cdn配置需要添加验证文件到服务器，所以我想通过其他方案进行解决。

### Nginx进行配置重定向

重定既可以解决http重定项https的问题，也可以解决访问主域名stacksweet.com不进行加速的问题。

解决思路： 1、将所有http访问都重定向到https协议，将所有没有带www的访问都重定向到https://www的网址

Nginx配置如下

```bash
# 在nginx.conf新增两条server配置

server {
	listen 80;
	server_name www.stacksweet.com stacksweet.com;
	return 301 https://www.stacksweet.com$request_uri;
}

server {
	listen 443;
	server_name stacksweet.com;
	return 301 https://www.stacksweet.comp$request_uri;
}
```

执行nginx -s reload 即可，这时访问任意的网址都能重定向到https://www的域名上来。



