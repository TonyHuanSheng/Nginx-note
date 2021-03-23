# Nginx-note
## 設定
### Nginx 相關檔案配置位置
- 所有設定檔: /etc/nginx/ 
- [主要設定檔](#主要設定檔): /etc/nginx/nginx.conf
- [預設設定檔](#預設設定檔): /etc/nginx/conf.d/default.conf
- [程序設定檔](#程序設定檔): /usr/sbin/nginx
- [log檔](#log檔): /var/logx/


<a name="主要設定檔"/>

## 主要設定檔

Nginx 服務的主要設定檔
```vim
vim /etc/nginx/nginx.conf
```
```nginx
# 啟用 Nginx 的 Linux 帳戶
user nginx;
# 啟用的執行續數量(建議搭配CPU核心數 * 2)
worker_processes auto; # worker_processes 1;
# log 檔位置
error_log /var/log/nginx/error.log warn;
pid       /var/run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;
＃加載動態模塊。 參見/usr/share/doc/nginx/README.dynamic。
包括/usr/share/nginx/modules/*.conf;

events {
  # 允許同一時間連線的數量
  worker_connections 1024;
}

http {
  # 預設 log 紀錄的格式
  log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';
  # access log 檔位置
  access_log /var/log/nginx/access.log main;
  
  sendfile            on;
  tcp_nopush          on;
  tcp_nodelay         on;
  keepalive_timeout   65;
  types_hash_max_size 2048;

  include             /etc/nginx/mime.types;
  default_type        application/octet-stream;
  
   # Load modular configuration files from the /etc/nginx/conf.d directory.
   # See http://nginx.org/en/docs/ngx_core_module.html#include
   # for more information.
   ＃從/etc/nginx/conf.d目錄加載模塊的配置文件。
   ＃參見http://nginx.org/en/docs/ngx_core_module.html#include
   ＃ 欲獲得更多信息。
   
   # 是否啟用 gaip壓縮 (預設為註解，不啟用)
   # gzip on;
   
   # 載入該路徑所有的設定檔，通常就是每個虛擬主機的配置
   include /etc/nginx/conf.d/*.conf;
   
   # 設定可上傳最大檔案容量(依需求設定)
   client_max_body_size 5m;
}
```
## 虛擬主機設定檔
```bash
vim /etc/nginx/conf.d/default.conf
```
```nginx
server{
  # 這個主機的 Port 或說監聽的Port
  listen  80;
  # 這個主機的名稱
  server_name localhost;
  # 設定預設編碼，但通常都是由網頁 <meta> 來定義，因此預設註解
  #charset koi8-r;
  # 針對這個主機的 log 檔位置
  #access_log  /var/log/nginx/log/host.access.log  main;
 
  # html 檔
  location / {
      # 網站的根目錄位置
      root   /usr/share/nginx/html;
      # 使用「瀏覽器」瀏覽根目錄時，未指定檔名時預設使用的檔案
      index  index.html index.htm;
   }
   
  # 發生 404 指定導向哪個網頁
  #error_page  404              /404.html;
  
  # redirect server error pages to the static page /50x.html
  #
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
      root   /usr/share/nginx/html;
   }
  # proxy the PHP scripts to Apache listening on 127.0.0.1:80
  #
  #location ~ \.php$ {
  #    proxy_pass   http://127.0.0.1;
  #}
 
  # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
  #
  # php 檔
  #location ~ \.php$ {
  #    root           html;
  #    fastcgi_pass   127.0.0.1:9000;
  #    fastcgi_index  index.php;
  #    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
  #    include        fastcgi_params;
  #}  
 
  # deny access to .htaccess files, if Apache's document root
  # concurs with nginx's one
  #
  #location ~ /\.ht {
  #    deny  all;
  #}
  #}
  
}
```
<a name="預設設定檔"/>

## 預設設定檔

<a name="程序設定檔"/>

## 程序設定檔

<a name="log檔"/>

## log檔
