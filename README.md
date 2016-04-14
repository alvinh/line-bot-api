# share-line-bot-api

### setup let's encrypt & install nginx @ centos
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
crontab -e 
# add below two lines
# 30 2 * * 1 /opt/letsencrypt/letsencrypt-auto renew >> /var/log/le-renew.log
# 30 2 * * 1 /usr/bin/systemctl reload nginx
```

### install node.js & npm @centos
```sh
curl https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
source ~/.bashrc
nvm install 5.0
nvm use 5.0
```
