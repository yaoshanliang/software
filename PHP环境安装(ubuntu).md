## 1、安装系统环境（基于Ubuntu 16.04）
* 更新系统

        apt-get update

* 安装git

        apt-get install git

        git --version
        #git version 2.7.4

 * 安装nginx 

        apt-get install nginx

        nginx -v
        #nginx version: nginx/1.10.3 (Ubuntu)

 * 安装mysql

        apt-get install mysql-server

        mysql -V
        #mysql  Ver 14.14 Distrib 5.7.18

* 安装php7.0

        apt-get install php7.0 php7.0-*
        apt-get install snmp

        php -v
        #PHP 7.0.18-0ubuntu0.16.04.1 (cli) ( NTS )

        # 修改php上传文件限制
        vi /etc/php/7.0/fpm/php.ini
        post_max_size = 20M
        upload_max_filesize = 20M

        # 重启fpm
        service php7.0-fpm restart

 * 安装composer

        php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
        php composer-setup.php
        php -r "unlink('composer-setup.php');"
        sudo mv composer.phar /usr/local/bin/composer
        composer -V
        # Composer version 1.4.2


## 2、部署代码

* clone 代码

        cd /var/www/html
        git clone https://git.coding.net/iat/example.git example

* 代码依赖

        cd example
        composer install

* 代码配置

        cd example
        chmod -R 777 storage
        chmod -R 777 bootstrap
        chmod -R 777 public/storage
        cp .env.example .env
        vi .env
        #修改对应的数据库配置

        php artisan key:generate
        php artisan migrate
        php artisan db:seed

* nginx 配置

        vi /etc/nginx/sites-available/default

        server {
            listen 80;

            root /var/www/html/example/public;
                  client_max_body_size 20M;
                  index index.php index.html index.htm index.nginx-debian.html;

            location / {
                try_files $uri $uri/ /index.php?$query_string;
            }

            location ~ \.php$ {
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
                include fastcgi_params;
            }
        }

        sudo service nginx restart
        
* 允许mysql远程访问

		vi /etc/mysql/mysql.conf.d/mysqld.cnf
		bind-address            = 0.0.0.0

    	# 登录mysql
    	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
    	FLUSH PRIVILEGES

* 访问

        前台：IP(域名)
        后台：IP(域名)/admin 账户：admin 密码：admin
