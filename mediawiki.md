# Mediawiki
Configuration used for mediawiki!

## Nginx config
nginx config for mediawiki, main config `/etc/nginx/sites-available/mediawiki`.

```
server {
        server_name domain.com;
        #listen 80;
	listen 443 ssl;

	ssl_certificate /etc/letsencrypt/live/domain.com/fullchain.pem;
	ssl_certificate_key /etc/letsencrypt/live/domain.com/privkey.pem;

        root /var/www/mediawiki/public_html;
        index index.php index.html index.htm;

        access_log /var/log/nginx/access-wiki.log;
        error_log /var/log/nginx/error-wiki.log;

        location ~ \.ht {
            deny all;
        }

        location / {
            try_files $uri $uri/ @rewrite;

            ##setup basic auth
            auth_basic "Restricted Content";
            auth_basic_user_file /etc/nginx/.htpasswd;

            ##deny from all except me
            #allow my-ip;
            #deny all;
        }

        location @rewrite {
            rewrite ^/(.*)$ /index.php;
        }

        location ^~ /maintenance/ {
            return 403;
        }

        location ~ \.php$ {
            include /etc/nginx/snippets/fastcgi-php.conf;

            fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        }
}
```

other config `/etc/nginx/sites-available/default`, to prevent using https to see cert, and access mediawiki.

```
server {
    listen 80 default_server;
    listen 443 default_server;
    server_name _;
    include snippets/snakeoil.conf;
    return 404;
}
```

## SSL
SSL configured with crontab - first run a manual config with standalone server (then it knows that to do next time).
```
43 6 * * * certbot renew --post-hook "systemctl reload nginx"
```
