### Đổi pass 
```
passwd root
```
### Đổi port 
```
vi /etc/ssh/sshd_config

/etc/init.d/ssh restart

ufw allow 40000/tcp
ufw allow 80/tcp
```
### Cập nhật 
```
apt update
```
### Đặt múi giờ
```
date
dpkg-reconfigure tzdata
```
### Cài nginx 
```
apt install nginx
ufw app list
ufw allow 'Nginx HTTP'
ufw status
ufw enable
ufw status
systemctl status nginx
ifconfig
/etc/init.d/nginx restart
``` 
Sửa cấu hình nginx /etc/nginx/nginx.conf
```
	worker_connections = số cpu * 1024
	server_tokens off;
	server_names_hash_bucket_size 64;
	server_name_in_redirect off;
```
Tạo vhost cho nginx (tham khảo cuối file)
 tạo xong phải tạo link để kích hoạt: ln -s /etc/nginx/sites-available/mrs.conf /etc/nginx/sites-enabled/ 


```
/etc/init.d/nginx restart
```

### Cài nodejs
```
echo "setup nodejs";
vi nodesource_setup.sh
```
===> copy nội dung https://github.com/snvn1511/public/blob/main/node_source.sh 

```
bash nodesource_setup.sh
apt install nodejs
node -v
```

### Cài Mongodb
```
apt install mongodb
mongo

use admin;

db.createUser(
   {
     user: "adminxxx",
     pwd: "xxxxx", 
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
   }
);
```

 
==================================================================
File vhost cho nginx,

```

#khong cho phep truy cap web bang ip
server {  
              listen 80;
              server_name IP_của_server_thay_thế_vào_đây;

              location / {                        
                     return 403; 
               }  
}

#web bang nodejs: Thay thế domain.com bằng tên miền thật 
server {
              listen 80;
              server_name domain.com;

              location / {

                    proxy_pass http://localhost:3000;
                    proxy_http_version 1.1;
                    proxy_set_header Upgrade $http_upgrade;
                    proxy_set_header Connection 'upgrade';
                    proxy_set_header Host $host;
                    proxy_cache_bypass $http_upgrade;

               }
}

 

### Tạo service chạy nodejs (có thể không cần)

vi /etc/systemd/system/taskwork.service

```
[Unit]
Description="Taskwork app"

[Service]
ExecStart=/usr/bin/nodemon /usr/bin/npm start
WorkingDirectory=/var/apps/taskwork-server
Restart=always
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=MyApp
Environment=NODE_ENV=production PORT=3000

[Install]
WantedBy=multi-user.target

```
