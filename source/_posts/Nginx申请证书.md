---
title: 手动配置Nginx证书并强制https
date: 2024-04-10 13:33:36
tags:
---

## 证书申请

### acme.sh安装

首先获取 `acme.sh` 脚本并安装

```shell
curl  https://get.acme.sh | sh
```

获取后，可以重新连接一下服务器，或者重开一个shell，会自动加载 `acme.sh` 命令，接下来可以直接使用`acme.sh` 命令。

接下来首先注册账号获取token，使用邮箱注册，这里随便输入邮箱即可

```shell
acme.sh --register-account -m youremail@example.com
```

### 证书申请

接下来申请证书，这里可以使用 `-d` 参数来指定域名，可以多个域名同时进行申请。

#### nginx申请

申请的方式有很多种，这里采用nginx的方式来申请。采用这种方式的前提是本机已经安装了nginx，且配置了对应域名的server。

```shell
acme.sh --issue  -d domain1.example.com  -d domain2.example.com  --nginx
```

#### 80端口申请

如果本机的80端口没有被占用，也可以采用本机80端口的方式。

```shell
acme.sh --issue  -d domain1.example.com  -d domain2.example.com  --standlone
```

#### DNS api申请

这里使用Cloudfare举例说明，更多的请参考 [文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) 。

```shell
export CF_Token="your_cf_api_token"
export CF_Zone_ID="your_domain_Zone_ID"
```

上面的`CF_Token`是在 [**User API Tokens** ](https://dash.cloudflare.com/profile/api-tokens)生成的，生成时可以选择此token的权限，这里仅仅为了申请DNS证书，选择Zone Template即可。

上述中的Zone_ID是当仅为一个域名生成的证书时所使用的，点开域名的Overview页面，可以在右下侧中看到此域名对应的Zone_ID。

当针对多个域名时，比如需要申请的域名中有`example1.com`，`test1.com`的域名时，一方面既需要在创建API Token时将两个域名都允许访问，另一方面，需要使用

```shell
export CF_Account_ID="your_accound_id"
```

来代替CF_Zone_ID。

当然也可以比较懒的方式采用Global Key的方式

```shell
export CF_Key="your_global_key"
export CF_Email="your_cf_email"
```

接下来就是申请证书

```shell
acme.sh --issue --dns dns_cf -d domain.example.com -d *.example.com
```

可以采用通配符的方式申请。

#### 安装证书

接下来为nginx安装证书，这里的安装证书本质上对证书文件进行copy和复制，参数中 `--key-file` 和 `--fullchain-file` 分别为 `nginx` 中的 `ssl_certificate_key` 和 `ssl_certificate` ，具体位置安装位置可以自行设定，这里我是默认安装到了 `/etc/nginx/certs/` 文件夹中。这个安装路径即为在 `nginx` 配置证书时的位置

```shell
acme.sh --install-cert  -d domain1.example.com  -d domain2.example.com  \
--key-file       /etc/nginx/certs/key.pem  \
--fullchain-file /etc/nginx/certs/cert.pem \
--reloadcmd     "service nginx force-reload"
```

## 配置nginx

下面配置为强制http跳转到https。

```shell
server { 
		listen 443 ssl;
        listen [::]:443 ssl;
        server_name domain1.example.com domain2.example.com;
		ssl_certificate    /etc/nginx/certs/cert.pem;
        ssl_certificate_key    /etc/nginx/certs/key.pem;
        ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;
        add_header Strict-Transport-Security "max-age=31536000";
    	error_page 497  https://$host$request_uri;
    	location / {
                proxy_pass  http://127.0.0.1:8080; 
                proxy_set_header Host $proxy_host; 
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
server {
        listen 80;
        listen [::]:80;
        server_name domain1.example.com domain2.example.com;
        return 301 https://$server_name$request_uri;
}
```



### 注意

在申请证书时，如果没有一条命令申请多个证书，那么在install时，需要一个一个安装到不同的位置，并在nginx中配置多个server来分别指定域名的证书位置。

比如运行了两条acme.sh命令，每次却只申请了一个证书，那么上述的install命令，如果输入两个证书，最终只会含有一个域名的证书，且如果运行两次install命令，但不修改两次的install位置，后面的install的证书会覆盖前面的，最终也只会有一个证书