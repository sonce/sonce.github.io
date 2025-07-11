---
title: XBoard安装
date: 2025-07-10 10:30:59
categories:
  - 科学上网
tags:
  - 科学上网
  - xboard
cover: https://img.wwwfans.xyz/python.jpg
feature: false
published: false
comments: true
image-auto-upload: true
---
## 安装
docker-compose.yaml
``` yaml
version: '3.8' # 确保版本号与你的 Docker Compose 版本兼容

services:
  web:
    image: ghcr.io/cedar2025/xboard:new
    # 移除了原先可能存在的针对 Redis socket 的 volumes:
    # - ./.docker/.data/redis/:/data/ (如果这个卷的唯一目的是为了Redis socket，则可以移除或确认其其他用途)
    volumes:
      - ./.env:/www/.env
      - ./data:/www/.docker/.data # 这个通用数据卷保留
      - ./storage/logs:/www/storage/logs
      - ./storage/theme:/www/storage/theme
      - ./plugins:/www/plugins
      - redis_shared_data:/mnt/redis_socket_data # 将共享卷挂载到 Xboard web 容器内的一个路径
    environment:
      - docker=true
    # depends_on: - redis # 移除对内置redis的依赖
    # network_mode: host # 保持你原有的设置，如果需要连接 localhost 上的 Redis，host模式可以，否则需要连接到redis_network
    command: php artisan octane:start --host=0.0.0.0 --port=7001
    # command: ["tail", "-f", "/dev/null"]
    restart: on-failure
    # ports:
    #   - "7001:7001"
    networks: # 将 Xboard 服务也加入到 redis_network (如果Redis不在host模式下运行且你想通过容器名连接)
      - web # 默认网络
      - internal
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.web.rule=Host(`yourdomain.com`,`sub.yourdomain.com`)"
      - "traefik.http.routers.web.entrypoints=websecure"
      - "traefik.http.routers.web.tls.certresolver=letsencrypt"
      - "traefik.http.services.web-service.loadbalancer.server.port=7001"


  horizon:
    image: ghcr.io/cedar2025/xboard:new
    # 移除了原先可能存在的针对 Redis socket 的 volumes:
    # - ./.docker/.data/redis/:/data/
    volumes:
      - ./.env:/www/.env
      - ./data:/www/.docker/.data # 这个通用数据卷保留
      - ./storage/logs:/www/storage/logs
      - ./plugins:/www/plugins
      - redis_shared_data:/mnt/redis_socket_data # 将共享卷挂载到 Xboard web 容器内的一个路径
    restart: on-failure
    # network_mode: host # 保持你原有的设置
    command: php artisan horizon
    # command: ["tail", "-f", "/dev/null"]
    # depends_on: - redis # 移除对内置redis的依赖
    networks:
      - web
      - internal

# 不再需要在此文件中定义 redis 服务
volumes:
  redis_shared_data:
    external: true

networks:
  web:
    external: true
  internal:
    external: true
```

``` shell
docker compose run -it --rm \
    -e ENABLE_SQLITE=true \
    -e ENABLE_REDIS=true \
    -e ADMIN_ACCOUNT=xxx@gmail.com \
    web php artisan xboard:install

dc up -d
```

## 更新
``` shell
cd Xboard
docker compose pull && \
docker compose run -it --rm web php artisan xboard:update && \
docker compose up -d
```
## 后端
### XrayR VMESS+TLS+CDN
   项目地址：https://github.com/XrayR-project/XrayR
``` shell
wget -N https://raw.githubusercontent.com/XrayR-project/XrayR-release/master/install.sh && bash install.sh
```
   VMESS+TLS+CDN
   配置地址 `/etc/XrayR/config.yaml`
``` json
Log:
  Level: warning # Log level: none, error, warning, info, debug 
  AccessPath: # /etc/XrayR/access.Log
  ErrorPath: # /etc/XrayR/error.log
DnsConfigPath: # /etc/XrayR/dns.json # Path to dns config, check https://xtls.github.io/config/dns.html for help
RouteConfigPath: # /etc/XrayR/route.json # Path to route config, check https://xtls.github.io/config/routing.html for help
InboundConfigPath: # /etc/XrayR/custom_inbound.json # Path to custom inbound config, check https://xtls.github.io/config/inbound.html for help
OutboundConfigPath: # /etc/XrayR/custom_outbound.json # Path to custom outbound config, check https://xtls.github.io/config/outbound.html for help
ConnectionConfig:
  Handshake: 4 # Handshake time limit, Second
  ConnIdle: 30 # Connection idle time limit, Second
  UplinkOnly: 2 # Time limit when the connection downstream is closed, Second
  DownlinkOnly: 4 # Time limit when the connection is closed after the uplink is closed, Second
  BufferSize: 64 # The internal cache size of each connection, kB
Nodes:
  -
    PanelType: "NewV2board" # Panel type: SSpanel, V2board, NewV2board, PMpanel, Proxypanel, V2RaySocks
    ApiConfig:
      ApiHost: "https://xboard url/"
      ApiKey: "xboard api key"
      NodeID: 1
      NodeType: V2ray # Node type: V2ray, Shadowsocks, Trojan, Shadowsocks-Plugin
      Timeout: 30 # Timeout for the api request
      EnableVless: false # Enable Vless for V2ray Type
      EnableXTLS: false # Enable XTLS for V2ray and Trojan
      SpeedLimit: 0 # Mbps, Local settings will replace remote settings, 0 means disable
      DeviceLimit: 0 # Local settings will replace remote settings, 0 means disable
      RuleListPath: # /etc/XrayR/rulelist Path to local rulelist file
    ControllerConfig:
      ListenIP: 0.0.0.0 # IP address you want to listen
      SendIP: 0.0.0.0 # IP address you want to send pacakage
      UpdatePeriodic: 60 # Time to update the nodeinfo, how many sec.
      EnableDNS: false # Use custom DNS config, Please ensure that you set the dns.json well
      DNSType: AsIs # AsIs, UseIP, UseIPv4, UseIPv6, DNS strategy
      EnableProxyProtocol: false # Only works for WebSocket and TCP
      AutoSpeedLimitConfig:
        Limit: 0 # Warned speed. Set to 0 to disable AutoSpeedLimit (mbps)
        WarnTimes: 0 # After (WarnTimes) consecutive warnings, the user will be limited. Set to 0 to punish overspeed user immediately.
        LimitSpeed: 0 # The speedlimit of a limited user (unit: mbps)
        LimitDuration: 0 # How many minutes will the limiting last (unit: minute)
      GlobalDeviceLimitConfig:
        Enable: false # Enable the global device limit of a user
        RedisAddr: 127.0.0.1:6379 # The redis server address
        RedisPassword: YOUR PASSWORD # Redis password
        RedisDB: 0 # Redis DB
        Timeout: 5 # Timeout for redis request
        Expiry: 60 # Expiry time (second)
      EnableFallback: false # Only support for Trojan and Vless
      FallBackConfigs:  # Support multiple fallbacks
        -
          SNI: # TLS SNI(Server Name Indication), Empty for any
          Alpn: # Alpn, Empty for any
          Path: # HTTP PATH, Empty for any
          Dest: 80 # Required, Destination of fallback, check https://xtls.github.io/config/features/fallback.html for details.
          ProxyProtocolVer: 0 # Send PROXY protocol version, 0 for dsable
      CertConfig:
        CertMode: none # Option about how to get certificate: none, file, http, tls, dns. Choose "none" will forcedly disable the tls config.
        CertDomain: "node1.test.com" # Domain to cert
        CertFile: /etc/XrayR/cert/node1.test.com.cert # Provided if the CertMode is file
        KeyFile: /etc/XrayR/cert/node1.test.com.key
        Provider: alidns # DNS cert provider, Get the full support list here: https://go-acme.github.io/lego/dns/
        Email: test@me.com
        DNSEnv: # DNS ENV option used by DNS provider
          ALICLOUD_ACCESS_KEY: aaa
          ALICLOUD_SECRET_KEY: bbb
   ```
`/etc/nginx/conf.d/nginx.conf`
加载conf.d目录下所有的配置
``` conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
```
`/etc/nginx/conf.d/xrayr.conf`
转发websocket请求到4443端口
``` config
server {
    server_name  yourdomain.com;

    location /helloworld {
        proxy_pass                       http://127.0.0.1:4443;
        proxy_redirect                   off;
        proxy_http_version               1.1;
        proxy_set_header Upgrade         $http_upgrade;
        proxy_set_header Connection      "upgrade";
        proxy_set_header Host            $http_host;
        proxy_set_header X-Real-IP       $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    listen 443 ssl;
    ssl_certificate /root/certs/yourdomain.com/fullchain.pem;
    ssl_certificate_key /root/certs/yourdomain.com/privkey.pem;
}
```
XBoard 新增节点
节点地址：youdomain.com
连接端口：443
服务端口：4443
TLS： 支持
传输协议：WebSocket
编辑协议：
```json
{
  "path": "/helloworld",
  "headers": {
    "Host": "your_domain"
  }
}
```

### V2bx
项目地址：https://github.com/wyx2685/V2bX
``` shell
wget -N https://raw.githubusercontent.com/wyx2685/V2bX-script/master/install.sh && bash install.sh
```
配置：`/etc/V2bX/config.json`
``` json
{
    "Log": {
        "Level": "debug",
        "Output": ""
    },
    "Cores": [{
        "Type": "hysteria2",
        "Log": {
            "Level": "error"
        }
    }, {
        "Type": "sing",
        "Log": {
            "Level": "error",
            "Timestamp": true
        },
        "NTP": {
            "Enable": false,
            "Server": "time.apple.com",
            "ServerPort": 0
        },
        "OriginalPath": "/etc/V2bX/sing_origin.json"
    }, {
        "Type": "xray",
        "Log": {
            "Level": "error",
            "ErrorPath": "/etc/V2bX/error.log"
        },
        "OutboundConfigPath": "/etc/V2bX/custom_outbound.json",
        "RouteConfigPath": "/etc/V2bX/route.json"
    }],
    "Nodes": [{
        "Core": "hysteria2",
        "ApiHost": "https://yourdomain.com",
        "ApiKey": "your_api_key",
        "NodeID": 10,
        "NodeType": "hysteria2",
        "Hysteria2ConfigPath": "/etc/V2bX/hy2config.yaml",
        "Timeout": 30,
        "ListenIP": "",
        "SendIP": "0.0.0.0",
        "DeviceOnlineMinTraffic": 200,
        "CertConfig": {
            "CertMode": "dns",
            "RejectUnknownSni": false,
            "CertDomain": "your_domain",
            "CertFile": "/etc/V2bX/fullchain.cer",
            "KeyFile": "/etc/V2bX/cert.key",
            "Email": "v2bx@github.com",
            "Provider": "cloudflare",
            "DNSEnv": {
                "CF_DNS_API_TOKEN": "xxxxxxxxxxxxxx"
            }
        }
    }, {
        "Core": "xray",
        "ApiHost": "https://yourdomain.com",
        "ApiKey": "your_api_key",
        "NodeID": 9,
        "NodeType": "vless",
        "Timeout": 30,
        "ListenIP": "0.0.0.0",
        "SendIP": "0.0.0.0",
        "DeviceOnlineMinTraffic": 200,
        "EnableProxyProtocol": true,
        "EnableUot": true,
        "EnableTFO": true,
        "DNSType": "UseIPv4",
        "CertConfig": {
            "CertMode": "none",
            "RejectUnknownSni": false,
            "CertDomain": "example.com",
            "CertFile": "/etc/V2bX/fullchain.cer",
            "KeyFile": "/etc/V2bX/cert.key",
            "Email": "v2bx@github.com",
            "Provider": "cloudflare",
            "DNSEnv": {
                "EnvName": "env1"
            }
        }
    }]
}

```

``` nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 1024;
	# multi_accept on;
}

stream {
    map $ssl_preread_server_name $backend_name {
        www.bing.com reality_backend;
        default web_backend;
    }

    upstream reality_backend {
        server 127.0.0.1:1443;
    }

    upstream web_backend {
        server 127.0.0.1:8443;
    }

    upstream hysteria_backend {
        server 127.0.0.1:2443;
    }    

    server {
        listen 443;
        listen [::]:443;

        ssl_preread    on;
        proxy_pass     $backend_name;
        proxy_protocol on;
    }

    server {
        listen 443 udp reuseport;
        listen [::]:443 udp reuseport;

        proxy_pass    hysteria_backend;
        proxy_timeout 20s;
    }
}

http {
    server_tokens off;
    include       mime.types;
    default_type  application/octet-stream;
    
    map $http_x_forwarded_for $clientRealIp {
        "" $remote_addr;
        "~*(?P<firstAddr>([0-9a-f]{0,4}:){1,7}[0-9a-f]{1,4}|([0-9]{1,3}\.){3}[0-9]{1,3})$" $firstAddr;
    }

    log_format main '$clientRealIp $remote_addr $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" $http_x_forwarded_for '
                    '"$upstream_addr" "$upstream_status" "$upstream_response_time" "$request_time" ';
    access_log       logs/access.log  main;

    sendfile          on;
    keepalive_timeout 65;

    gzip       on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    server {
        listen 80;
        listen [::]:80;
        return 301 https://$host$request_uri;
    }

    server {
        listen 127.0.0.1:8443 quic reuseport;
        listen 127.0.0.1:8443 ssl proxy_protocol reuseport;

        http2 on;

        set_real_ip_from 127.0.0.1;
        real_ip_header   proxy_protocol;

        ssl_certificate     /etc/V2bX/fullchain.cer;
        ssl_certificate_key /etc/V2bX/cert.key;

        ssl_protocols             TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;
        ssl_ciphers               ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-ECDSA-CHACHA20-POLY1305;
        ssl_ecdh_curve            secp521r1:secp384r1:secp256r1:x25519;

        # 替换为自己 XHTTP 的路径，一般为随机字符串，对应下文中 Xray 的配置
        location /helloworld {
            grpc_pass grpc://127.0.0.1:2024;
            grpc_set_header Host $host;
            grpc_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        location / {
            add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
            add_header Alt-Svc 'h3=":443"; ma=86400';

            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   Host $host;
            proxy_pass         http://127.0.0.1:3001;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection "upgrade";
        }
    }
}

```
