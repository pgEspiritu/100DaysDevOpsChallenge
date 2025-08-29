# 🧪 100 Days of DevOps – Day 20
## ✅ Task: Configure NGINX with PHP-FPM using Unix Socket

```text
The Nautilus application development team is planning to launch a new PHP-based application,
which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting 
with the production support team and they have shared some requirements regarding the infrastructure. 
Below are the requirements they shared:

a. Install nginx on app server 3 , configure it to use port 8099 and its document root should be /var/www/html.
b. Install php-fpm version 8.1 on app server 3, it must use the unix socket /var/run/php-fpm/default.sock (create the parent directories if don't exist).
c. Configure php-fpm and nginx to work together.
d. Once configured correctly, you can test the website using curl http://stapp03:8099/index.php command from jump host.
```

---

tasks:

- SSH into App Server 3.
- Install nginx.
- Configure nginx to listen on port 8099 with document root /var/www/html.
- Install PHP-FPM 8.1 and configure it to use Unix socket /var/run/php-fpm/default.sock.
- Update PHP-FPM socket permissions so nginx can connect.
- Configure nginx and PHP-FPM to work together.
- Create a test PHP file.
- Verify using curl http://stapp03:8099/index.php from jump host.

---

### 🔁 Step 1: SSH into App Server 3
```bash
ssh banner@stapp03
```

password:
```bash
BigGr33n
```

then log-in as root user
```bash
sudo su -
```

---

### 🔁 Step 2: Install NGINX
```bash
yum install -y nginx
```

---

### ⚙️ Step 3: Configure NGINX on Port 8099
Edit configuration file: /etc/nginx/nginx.conf
```bash
vi /etc/nginx/nginx.conf
```

then edit the configuration from this
```nginx
    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
```

to this

```nginx
      server {
          listen 8099;
          listen [::]:8099;
          server_name _;
          root /var/www/html;
          index index.php index.html;

          # Load configuration files for the default server block.
          include /etc/nginx/default.d/*.conf;
      
          error_page 404 /404.html;
          location = /404.html {
          }
  
          error_page 500 502 503 504 /50x.html;
          location = /50x.html {
          }
      }
```

then reload and check the status of the nginx
```bash
systemctl restart nginx
systemctl status nginx
```
> nginx is now active (running)

---

### 🔁 Step 4: Install PHP-FPM 8.1
Check first the OS version to know the repo needed.
```bash
cat /etc/os-release
```
> CentOS Stream version 9 is our OS

Ensure your system is up to date by running the following command:
```bash
dnf update
```
Install the Remi repository by running the following command:
```bash
dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
```

Check the available PHP modules and enable the Remi repository 8.1
```bash
dnf module list php
dnf module enable php:remi-8.1
```

install PHP core components:
```bash
dnf install php81 php81-php-fpm php81-php-cli
```

install PHP PHP-FPM
```bash
dnf install -y php php-fpm
```

then verify the version
```bash
php -v
```
> Now the remo repository version is 8.1

Check this site for guide in installing PHP 8.1 in CentOS 9
> [https://reintech.io/blog/setting-up-php-fpm-centos-9](https://reintech.io/blog/setting-up-php-fpm-centos-9) &
> [https://devtutorial.io/how-to-install-php-8-1-on-centos-stream-9-p3437.html](https://devtutorial.io/how-to-install-php-8-1-on-centos-stream-9-p3437.html)

---

### ⚙️ Step 5: Configure PHP-FPM to Use Unix Socket

Edit /etc/php-fpm.d/www.conf
```bash
vi /etc/php-fpm.d/www.conf
```

then change this
```ini
listen = /var/run/php-fpm/default.sock
user = nginx
group = nginx
```

then add this to the configuration file: /etc/nginx/nginx.conf
```ini
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
```

---

### 🔁 Step 6: Check status of PHP-FPM then restart NGINX and PHP-FPM
```bash
systemctl status php-fpm
systemctl restart nginx
systemctl restart php-fpm
```

---

### 🔁 Step 7: Verify from Jump Host
On Jump Host:
```bash
curl http://stapp03:8099/index.php
```
> It displays the website content (not in html raw file)

---

## ✅ Task Completed
- Installed and configured NGINX on App Server 3 (port 8099, root /var/www/html).
- Installed PHP-FPM 8.1, configured it to use /var/run/php-fpm/default.sock.
- Integrated NGINX + PHP-FPM.
- Verified successfully with curl.

