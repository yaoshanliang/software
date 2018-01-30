### 1、查看nginx所属用户

    vi /etc/nginx/nginx.conf
    
    user www-data;
    worker_processes 4;
    pid /run/nginx.pid;

  可以看到用户为www-data(注意：接下来的权限都要配置为该用户）

### 2、创建web服务器用户目录

    sudo mkdir /var/www/html/.ssh
    sudo chown -R www-data:www-data /var/www/html/.ssh/
 
### 3、修改web目录用户权限

    sudo chown -R www-data:www-data /var/www/html
  
### 4、准备钩子文件
 
 在web目录html下建立一个目录githook, 里面放上一个php文件index.php，并确保该项目可以被外部访问，如`http://example.com/githook/index.php`
  
    <?php

        error_reporting(1);

        $root = '/var/www/html/'; // 服务器目录
        $token = 'coding'; // token
        $wwwUser = 'www-data'; //nginx所属用户
        $wwwGroup = 'www';

        $json = json_decode(file_get_contents('php://input'), true);

        if (empty($json['token']) || $json['token'] !== $token) {
            exit('error request');
        }

        if (! ($repo = $_GET['repo'])) {
            $repo = $json['repository']['name'];
        }

        $folder = $root . $repo;

        $cmd = "cd $folder && git pull";

        $res = shell_exec($cmd);

        var_dump($res);

  
### 5、生成部署公钥

  粘贴公钥至git平台（如：coding，注意：粘贴至部署公钥，非个人公钥）
  
    sudo -Hu www-data ssh-keygen -t rsa # 请选择 "no passphrase"，一直回车下去
    sudo cat /var/www/html/.ssh/id_rsa.pub
  

  
 
### 6、clone项目
    
  我们需要先在服务器上clone一次，以后都可以实现自动部署了：
  
    sudo -Hu www-data git clone git@git.coding.net:you/repo.git
  
### 7、测试

  本地推送代码，看看线上有没有自动部署
