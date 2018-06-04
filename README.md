# ubuntu-16.04-server-checklist
my checklist for creating new ubuntu 16.04 server

## Initial config
1. Login as root
1. Add new user: `adduser imper`
1. Add root privileges: `usermod -aG sudo imper` and logout
1. Copy your public key from your machine: `ssh-copy-id imper@serverhost`
1. Login as imper
1. Add autocompletion for bash with: `sudo apt-get update` `sudo apt-get install bash-completion` and logout/login again
1. Install nano `sudo apt-get install nano`
1. Edit sshd config: `sudo nano /etc/ssh/sshd_config` and set `PasswordAuthentication no`
1. Reload sshd: `sudo systemctl reload sshd`
1. Setup firewall:
    1. Install ufw: `sudo apt-get install ufw`
    1. `sudo ufw allow OpenSSH`
    1. `sudo ufw enable`
    1. Check status with `sudo ufw status`
  
## Additional steps for initial
1. Configure timezones with `sudo dpkg-reconfigure tzdata`
1. Install NTP for time synchronization `sudo apt-get install ntp`

## Nginx
1. `sudo apt-get install nginx`
1. `sudo ufw allow 'Nginx Full'`

## MariaDB
1. Visit https://downloads.mariadb.org/mariadb/repositories/#mirror=kisiek for actual repository instructions
1. `mysql_secure_installation`
1. Login to mysql with `sudo mysql -p`
1. Add new user `CREATE USER 'imper'@'localhost' IDENTIFIED BY 'password';`
1. `GRANT ALL PRIVILEGES ON * . * TO 'imper'@'localhost';`
1. `FLUSH PRIVILEGES;` and `exit`

## PHP
1. Add ondrej repos: `sudo add-apt-repository ppa:ondrej/php`
1. `sudo apt-get update`
1. `sudo apt-get install php7.2-fpm php7.2-mysql`
1. Additionals: `sudo apt-get install php7.2-bcmath php7.2-bz2 php7.2-cgi php7.2-curl php7.2-gd php7.2-imap php7.2-intl php7.2-mbstring php7.2-soap php7.2-xml php7.2-xmlrpc php7.2-zip`
1. Imagick: `sudo apt-get install php-imagick`

## Let's encrypt
1. Installation:
    1. `sudo add-apt-repository ppa:certbot/certbot`
    1. `sudo apt-get update`
    1. `sudo apt-get install python-certbot-nginx`
1. Obtain cert with: `sudo certbot --nginx -d example.com -d www.example.com`
1. Add auto renewal to crontab:
    1. `sudo crontab -e`
    1. Add `30 2 * * 1 certbot renew >> /var/log/certbot-renew.log`
    1. Add `35 2 * * 1 systemctl reload nginx`
  
## Node.js
1. `sudo apt-get install curl`
1. `curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh`
1. `sudo bash nodesource_setup.sh`
1. `sudo apt-get install nodejs`
1. `sudo apt-get install build-essential`

## Yarn
1. `curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -`
1. `echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list`
1. `sudo apt-get update && sudo apt-get install yarn`

## Composer
1. `wget https://getcomposer.org/installer`
1. `php installer`
1. `sudo mv composer.phar /usr/local/bin/composer`

## CacheTool
1. `curl -sO http://gordalina.github.io/cachetool/downloads/cachetool.phar`
1. `chmod +x cachetool.phar`
1. `sudo mv cachetool.phar /usr/local/bin/phpcachetool`
1. Create config file under `/etc/cachetool.yml`
    1. `adapter: fastcgi`
    1. `fastcgi: /run/php/php7.2-fpm.sock`

# Configs
## PHP config
1. `sudo nano /etc/php/7.2/fpm/php.ini`
    1. `cgi.fix_pathinfo=0`
    1. `opcache.memory_consumption=256`
    1. `opcache.max_accelerated_files=20000`
    1. `opcache.validate_timestamps=0`
    1. `realpath_cache_size=4096K`
    1. `realpath_cache_ttl=600`
    1. `max_execution_time = 180`
    1. `max_input_time = 180`
    1. `memory_limit = 512M`
    1. `post_max_size = 100M`
    1. `upload_max_filesize = 100M`
    1. `default_socket_timeout = 120`
    1. `pdo_mysql.cache_size = 4000`
    1. `mysqli.cache_size = 4000`
    1. `session.sid_length = 40`
    1. `session.sid_bits_per_character = 4`
    1. `mbstring.language = Polish`
    1. `soap.wsdl_cache_enabled=0`
    1. `opcache.enable=1`
    1. `opcache.enable_cli=1`
1. `sudo nano /etc/php/7.2/fpm/pool.d/www.conf`
    1. `user = imper`
    1. `group = imper`
    1. `pm.max_children = 20`
    1. `pm.start_servers = 5`
    1. `pm.min_spare_servers = 5`
    1. `pm.max_spare_servers = 20`
    1. `slowlog = /var/log/php.$pool.log.slow`
    1. `request_slowlog_timeout = 10s`
    
  
## Nginx config
1. `sudo nano /etc/nginx/nginx.conf`
    1. `user imper`

## MariaDB config
1. `sudo nano /etc/mysql/my.cnf`
    1. `query_cache_limit               = 4M`
    1. `query_cache_size                = 128M`
    1. `innodb_buffer_pool_size = 512M`
    1. Under [mysqld] insert:
        * `character-set-server  = utf8mb4`
        * `collation-server      = utf8mb4_polish_ci`
        * `innodb_ft_min_token_size = 1`
        * `ft_min_word_len = 1`
