1. docker-lnmp 项目帮助开发者快速构建本地开发环境，包括Nginx、PHP、MySQL、Redis 服务镜像，支持配置文件和日志文件映射，不限操作系统；
2. 此项目适合个人开发者本机部署，可以快速切换服务版本满足学习服务新版本的需求； 也适合团队中统一开发环境，设定好配置后一键部署， 便于提高团队开发效率；
3. PHP 支持多版本 包括php5.6、 php7.1、php7.2、php7.3、php7.4、php8.0、php8.1、php8.2、php8.3、php8.4 版本；
4. MySQL 支持 5.7 、8.0 版本；
5. Redis 支持 4.0 、5.0 、6.0 版本；
6. PHP 扩展包括了gd、grpc、redis、protobuf、memcached、swoole、kafka、imap等；

### 一. [install docker](https://docs.docker.com/engine/install/)

```shell
docker -v
docker-compose -v

```
### 二. download
```shell
git clone https://github.com/kanhuji/docker-lnmp.git
```
### 三. init
```shell
cd docker-lnmp
cp .env.example .env
```

### 四. run
```shell
docker network ls | grep backend

docker compose up -d nginx php83 mysql redis ftp
docker ps
```

### 五. test
```shell
cp nginx/conf.d/default.conf.example nginx/conf.d/default.conf
docker-compose restart nginx

#绑定本机hosts
127.0.0.1 default.dev.com

```
访问 http://default.dev.com/ 得到响应 Hello Kanhuji! 表示运行成功。

### 六. note
    默认版本为：
    MySQL 5.7
    Redis 5.0
    可以通过修改 env 文件的 MYSQL_VERSION 、REDIS_VERSION 来选择其他版本
    MySQL 和 Redis 切换版本时，注意切换配置文件

    项目目录默认为 docker-lnmp/../www 目录
    可以通过修改 env 文件的 WEB_ROOT_PATH 来指定其他目录

    nginx 虚拟主机配置文件在 docker-lnmp/nginx/conf.d 目录内， 可以参考 default 项目配置。

### 七. restart | down | rebuild

```shell
#修改配置文件后重启即可
docker-compose restart nginx php

# 修改 dockerfile 或者 .env 文件之后 rebuild 可生效
docker compose up -d --build nginx php83 mysql redis ftp

# 停止
docker-compose stop

# 停止并删除容器
docker-compose down

# 停止并删除容器+镜像
docker-compose down --rmi all

```

### Certbot 申请免费的ssl证书
1. 先配置http可访问， 以 test.ogenes.cn 为例
```shell
[root@ogenes01 docker-lnmp]# pwd
/data/docker-lnmp
[root@ogenes01 docker-lnmp]# vim nginx/conf.d/test.conf
server {
    listen 80;
    listen [::]:80;

    server_name test.ogenes.cn;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        charset utf-8;
        default_type text/html;
        return 200 'Hello Ogenes Test!';
    }
}

[root@ogenes01 docker-lnmp]# docker-compose restart nginx
[+] Running 1/1
 ⠿ Container nginx  Started
[root@ogenes01 docker-lnmp]# curl test.ogenes.cn
Hello Ogenes Test!
```
2. 申请ssl证书
```shell
[root@ogenes01 docker-lnmp]# docker-compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d test.ogenes.cn
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for test.ogenes.cn

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/test.ogenes.cn/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/test.ogenes.cn/privkey.pem
This certificate expires on 2023-07-18.
These files will be updated when the certificate renews.

NEXT STEPS:
- The certificate will need to be renewed before it expires. Certbot can automatically renew the certificate in the background, but you may need to take steps to enable that functionality. See https://certbot.org/renewal-setup for instructions.
We were unable to subscribe you the EFF mailing list because your e-mail address appears to be invalid. You can try again later by visiting https://act.eff.org.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
[root@ogenes01 docker-lnmp]#
```

3. 修改nginx配置，支持https
```shell
[root@ogenes01 docker-lnmp]# vim nginx/conf.d/test.conf
server {
    listen 80;
    listen [::]:80;

    server_name test.ogenes.cn;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://test.ogenes.cn$request_uri;
    }
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name test.ogenes.cn;

    ssl_certificate /etc/nginx/ssl/live/test.ogenes.cn/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/test.ogenes.cn/privkey.pem;

    location / {
        charset utf-8;
        default_type text/html;
        return 200 'Hello Ogenes Test Https!';
    }
}
[root@ogenes01 docker-lnmp]# docker-compose restart nginx
[+] Running 1/1
 ⠿ Container nginx  Started
[root@ogenes01 docker-lnmp]# curl https://test.ogenes.cn
Hello Ogenes Test Https!
```
![image-20230419175534350](https://img.ogenes.cn/img/2023/image-20230419175534350.png)

4. 配置计划任务，每个月月初自动刷新
```shell
#更新https证书
1 1 1 * * cd /data/docker-lnmp && docker-compose run --rm certbot renew >> /dev/null 2>&1
```
