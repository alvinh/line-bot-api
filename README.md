# line-bot-api

### environment
  - OS: CentOS 7
  - Frontend Sever: Nginx
  - Backend Server: Node.js

### setup let's encrypt & install nginx
```sh
yum -y install git bc
git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt
yum -y install epel-release
yum -y install nginx
vi /etc/nginx/default.d/le-well-known.conf
systemctl restart nginx
cd /opt/letsencrypt/
./letsencrypt-auto certonly -a webroot --webroot-path=/usr/share/nginx/html -d asmobile.cc -d www.asmobile.cc
ls -l /etc/letsencrypt/live/asmobile.cc/
openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
vi /etc/nginx/conf.d/ssl.conf
vi /etc/nginx/default.d/ssl-redirect.conf
systemctl reload nginx
systemctl enable nginx
echo "30 2 * * 1 /opt/letsencrypt/letsencrypt-auto renew >> /var/log/le-renew.log" | tee -a /var/spool/cron/root
echo "30 2 * * 1 /usr/bin/systemctl reload nginx" | tee -a /var/spool/cron/root
```

### install node.js & npm
```sh
curl https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
source ~/.bashrc
nvm install 5.0
nvm use 5.0
# install forever to manage node process
npm install forever -g
```

### configuring nginx and ssl with node.js 
```sh
vi /etc/nginx/conf.d/ssl.conf
# modify location / {} block to below
location / {
         # THESE ARE IMPORTANT
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # This is what tells Connect that your session can be considered secure,
        # even though the protocol node.js sees is only HTTP:
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_read_timeout 5m;
        proxy_connect_timeout 5m;
        proxy_pass http://localhost:81;
        proxy_redirect off;
}
# reload nginx
nginx -s reload
```
