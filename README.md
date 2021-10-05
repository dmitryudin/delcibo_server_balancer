# delcibo_server

Это проект сервера логики delcibo<br/>
Начнём с установки балансировщика Nginx<br/>
```
sudo apt install nginx
```
Конфигурация Nginx /etc/nginx/nginx.conf
```
upstream cache-api {
    ip_hash;
    server 10.32.18.7:8080 max_fails=2 fail_timeout=10s;
    server 10.32.18.6:8080 max_fails=2 fail_timeout=10s;
    server 10.32.18.8:8080 max_fails=2 fail_timeout=10s;
}

server {
    listen 443 ssl http2;
    server_name cache-api.sample.com;
    access_log /var/log/nginx/cache-api-access.log upstream;
    error_log /var/log/nginx/cache-api-error.log;

    ssl_certificate /etc/ssl/sample.com.crt;
    ssl_certificate_key /etc/ssl/sample.com.key;

    root /var/www/html;

    location / {
    proxy_pass http://cache-api/;
    proxy_read_timeout 15;
    proxy_connect_timeout 3;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
    }
}
```
После обновления конфигурации, Nginx нужно перезапустить командой:
```
nginx -s reload
```
Далее развертываем mysql-server командой 
```
sudo apt-get install mysql-server
```
