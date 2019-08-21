### 关闭nginx

    service nginx stop

### 下载certbot工具，生成证书

    git clone https://github.com/certbot/certbot
    cd certbot
    ./letsencrypt-auto certonly --standalone --email your@qq.com -d your.domain.com
    # 记得修改 your.domain.com 为你的域名

### 配置nginx

    listen 443 ssl;
    listen [::]:443 ssl ipv6only=on;

    ssl_certificate /etc/letsencrypt/live/your.domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your.domain.com/privkey.pem;
    service nginx start


### 自动更新证书

    # 新建cron，每月1号更新一次证书
    * * 1 * * ./certbot-auto renew --pre-hook "service nginx stop" --post-hook "service nginx start"

    # --pre-hook 这个参数表示执行更新操作之前要做的事情，因为我有 --standalone 模式的证书，所以需要 停止 nginx 服务，解除端口占用。
    # --post-hook 这个参数表示执行更新操作完成后要做的事情，这里就恢复 nginx 服务的启用
