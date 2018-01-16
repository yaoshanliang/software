### 1、安装php70 php70-fpm

`brew install php70 --without-apache --with-fpm`

安装完成后，加入全局变量

`vim ~/.bash_profile`

添加

`export PATH=/usr/local/bin:/usr/sbin:$PATH`

然后

`vim ~/.bashrc`

添加

`export PATH = "$(brew --prefix homebrew/php/php70)/bin:$PATH"`

最后

`source ~/.bash_profile`
`source ~/.bashrc`

若需要加入开机自启
```
mkdir -p ~/Library/LaunchAgents
cp /usr/local/opt/php70/homebrew.mxcl.php70.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php70.plist   

# launchctl unload  ~/Library/LaunchAgents/homebrew.mxcl.php70.plist // 取消开机启动
```

1）修改php-fpm启动用户，否则会出现403

 `/usr/local/etc/php/7.0/php-fpm.d/www.conf`
 
 默认user、group为_www，需要改为当前用户、当前组

```
user = 当前用户
group = 当前组

```

2）php-fpm 默认`listen = 127.0.0.1:9000`

若出现端口占用，则需要修改对应端口。

3）每次修改了php.ini文件。都要重启php-fpm, 就像启动apache一样，不过这会不用重启nginx
重启步骤

`sudo killall php-fpm # 先停掉所有php-fpm的进程`

`cd /usr/local/Cellar/php70/7.0.18_10/
sbin # 目录会有php70-fpm`

`sudo ./php70-fpm start # 启动php-fpm`

### 2、安装 Nginx

`brew install nginx`

开机自启

```
mkdir -p ~/Library/LaunchAgents
cp /usr/local/opt/nginx/homebrew.mxcl.nginx.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist

# launchctl unload  ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist // 取消开机启动
```

nginx 配置文件在

`/usr/local/etc/nginx/nginx.conf`

添加一个配置

```
server {
        server_name  laravel.dev;
        listen 80;

        root /Users/iat/workspace/code/docker/laravel/public;

        location / {
            try_files $uri  /index.php$is_args$args; 
        }

        location ~ \.php$ {
            include        fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
        }
    }
```

nginx命令

```
nginx -t # 测试配置是否有语法错误
nginx # 启动nginx
nginx -s reload | reopen | stop | quit # 重新加载配置|重启|停止|退出

# 如果提示pid丢失的话，就用这句话
nginx -c /usr/local/etc/nginx/nginx.conf
```

### 3、安装mysql

`brew install mysql`

开机启动

```
cp /usr/local/opt/mysql/homebrew.mxcl.mysql.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist

# lauchctl unload ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist // 取消开机启动
```

### 附

#### php版本切换公具

`brew install php-version`

安装完成后 

`vim ~/.bashrc`

添加

```source $(brew --prefix php-version)/php-version.sh && php-version 7```

后面的 `&&php-version`是你想默认开启的php版本。

`sourc .bashrc`后，命令行输入`php-version`,就可以看到所有php版本，想用哪个就php-version 版本号 来切换版本

#### 安装php扩展包

`brew install php70-xdebug`

`brew search php70`可以看到所有php70的扩展库。
