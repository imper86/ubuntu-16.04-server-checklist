```
server {
        server_name domain.com www.domain.com;

        root /home/imper/sites/domain.com;
        index index.php index.html;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.2-fpm.sock;
        }

        location ~ /.well-known {
                allow all;
        }
}
```
