# 🧪 100 Days of DevOps – Day 19
## ✅ Task: Install and Configure Web Application

```text
xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter.
The development of these websites is still in-progress, but we want to get the servers ready.
Please perform the following steps to accomplish the task:

a. Install httpd package and dependencies on app server 3.
b. Apache should serve on port 6300.
c. There are two website's backups /home/thor/blog and /home/thor/games on jump_host.
Set them up on Apache in a way that blog should work on the link http://localhost:6300/blog/
and games should work on link http://localhost:6300/games/ on the mentioned app server.
d. Once configured you should be able to access the website using curl command on the respective app server, i.e curl http://localhost:6300/blog/ and curl http://localhost:6300/games/
```

---

tasks:
- SSH into App Server 3.
- Install httpd package and its dependencies.
- Update Apache configuration to listen on port 6300.
- Copy website backups from /home/thor/blog and /home/thor/games on jump_host to App Server 3.
- Configure Apache to serve:
    - /blog → accessible at http://localhost:6300/blog/
    - /games → accessible at http://localhost:6300/games/
- Restart Apache service to apply changes.
- Verify configuration using:
    - curl http://localhost:6300/blog/
    - curl http://localhost:6300/games/

---

### 🔁 Step 1: SSH into App Server 3
```bash
ssh banner@stapp03
```

password:
```bash
BigGr33n
```

---

### 🔁 Step 2: Install Apache (httpd)
```bash
sudo yum install -y httpd
sudo systemctl enable httpd
```

and verify the status:
```bash
sudo systemctl status httpd
```

> The Apache is not enabled but not yet active

---

### ⚙️ Step 3: Configure Apache to Run on Port 6300
Edit Apache main config:
```bash
sudo vi /etc/httpd/conf/httpd.conf
```

Find:
```bash
Listen 80
```

change to:
```bash
Listen 6300
```

Save and exit (esc, then type `:wq` to save and exit)

---

restart Apache
```bash
sudo systemctl restart httpd
```

Verify
```bash
sudo ss -tulnp | grep httpd
```
> now it Apache listen to `6300`

---

### 🔁 Step 4: Copy Website Backups
From jump host transfer backups to App Server 3:
```bash
scp -r /home/thor/blog /home/thor/games banner@stapp03:/tmp/
```

Then move into Apache document root:
```bash
sudo cp -r /tmp/blog /var/www/html/
sudo cp -r /tmp/games /var/www/html/
```

---

### ⚙️ Step 5: Configure Aliases in Apache
Edit configuration:
```bash
sudo vi /etc/httpd/conf/httpd.conf
```

Add at the bottom:
```apache
Alias /blog "/var/www/html/blog"
<Directory "/var/www/html/blog">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

Alias /games "/var/www/html/games"
<Directory "/var/www/html/games">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

Restart Apache:
```bash
sudo systemctl restart httpd
```

---

### 🔁 Step 6: Verify with curl
```bash
curl http://localhost:6300/blog/
curl http://localhost:6300/games/
```
> Both return the HTML content of the respective static sites ✅

---

## ✅ Task Completed
- Apache installed on App Server 3.
- Port changed to 6300.
- Blog & Games sites deployed under /var/www/html/.
- Aliases configured for /blog and /games.
- Verified successfully with curl.
