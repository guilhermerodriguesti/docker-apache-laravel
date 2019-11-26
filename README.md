<h1 align="center">Laravel em Container.</h1>

## Tecnologias

* PHP 7.3
* Apache 2
* Laravel

## 🚨 Pré-requisitos

* Instalar o NGINX no host local(hospedeiro);
* Configure o `vhost` do NGINX;
* Instalar o Sistema DOCKER;

## 🔧 Instruções de Instalação

1.  Configurar o NGINX do hospedeiro de acordo com abaixo:

Criar o `default.conf` no `/etc/nginx/conf.d/`

```
server {
    listen       80;
    server_name  portainer;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        proxy_pass http://127.0.0.1:9000;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
Criar o `laravel.conf` no `/etc/nginx/conf.d/`e configure o `server_name` e  `proxy_pass` de acordo com o seu app.
```
 # /var/docker/container-laravel
    server {
        listen 80;
        proxy_connect_timeout       600;
        proxy_send_timeout          600;
        proxy_read_timeout          600;
        send_timeout                600;

        server_name laravel.com.br;

        access_log /var/log/nginx/laravel-app.access.log;
        error_log /var/log/nginx/laravel-app.error.log;

        location / {
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   Host      $http_host;
            proxy_pass         http://127.0.0.1:8001;
        }
    }


 # /var/docker/container-laravel
 # Para funcional SSL crie o diretorio:
 # mkdir -p /etc/nginx/ssl/
 # coloque os certificados
 # e descomente as linhas do SSL


    server {
        listen 443 ssl;
        proxy_connect_timeout       600;
        proxy_send_timeout          600;
        proxy_read_timeout          600;
        send_timeout                600;

        #ssl    on;
        #ssl_certificate        /etc/nginx/ssl/youWildCard.pem;
        #ssl_certificate_key    /etc/nginx/ssl/you-server.key;

        server_name laravel.com.br;

        access_log /var/log/nginx/laravel-app.access.log;
        error_log /var/log/nginx/laravel-app.error.log;

        location / {
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   Host      $http_host;
            proxy_set_header   Access-Control-Allow-Origin 'https://laravel.com.br';
            proxy_pass         http://127.0.0.1:8001;
        }
    }

```

2.  Clonar este repositório;

3.  Executar `docker-compose build` dentro da pasta que fez o clone deste projeto.

4.  Executar `docker-compose up -d` dentro da pasta que fez o clone deste projeto..

## Como utitilizar o Container

Entre no Container
```
docker exec -it container-laravel bash
```
Para primeira instalacao remover o `composer.lock`

```
rm -rf composer.lock 
```
```
php -v
```
Dê permissão na pasta `storage` e na pasta `bootstrap/cache` e Execute na raiz: `composer install`

```
chmod -R 777 storage
```
```
chmod -R 777 bootstrap/cache
```
```
composer install
```
* Entre no diretorio raiz do laravel e copie o `.env.exemple` para `.env`

```
cp .env.example .env
```
```
vim .env
```
Execute também: `php artisan key:generate`
```
docker exec -it container-laravel php artisan key:generate
```
* Entre no diretorio e copie o arquivo .env.exemple para o arquivo .env.

* No Host hospedeiro reinicie o Nginx: `systemctl restart nginx`

## Verificar logs do Servidor(Host hospedeiro) NGINX!

```
tail -f /var/log/nginx/laravel-app.access.log
```
e
```
tail -f /var/log/nginx/laravel-app.error.log
```

## Verificar logs do Container APACHE!
```
tail -f container-laravel/logs/apache2/apache-laravel-error_log
```
e
```
tail -f container-laravel/logs/apache2/apache-laravel-access_log
```

## 🤝 Contribuições

Contribuições, problemas e solicitações de recursos são bem-vindos.<br />

## Author

👤 **Guilherme Rodrigues**

## 📝 Licença

Copyright © 2019 Community.<br />