# 🧪 100 Days of DevOps – Day 15 

## ✅ Setup SSL for Nginx

```text
The system admins team of xFusionCorp Industries needs to deploy a
new application on App Server 2 in Stratos Datacenter.
They have some pre-requites to get ready that server for application deployment.
Prepare the server as per requirements shared below:

1. Install and configure nginx on App Server 2.
2. On App Server 2 there is a self signed SSL certificate and
key present at location /tmp/nautilus.crt and /tmp/nautilus.key.
Move them to some appropriate location and deploy the same in Nginx.
3. Create an index.html file with content Welcome! under Nginx document root.
4. For final testing try to access the App Server 2 link (either hostname or IP)
from jump host using curl command. For example curl -Ik https://<app-server-ip>/.
```

---

### Tasks

1. Install and configure Nginx.
2. Deploy the provided SSL certificate and key.
3. Create index.html with Welcome!.
4. Verify access with curl -Ik https://<app-server-2-ip>/.

---

### 🔁 Step 1: SSH into App Server 2
From the jump host:
```bash
ssh steve@stapp02
```

Password:
```bash
Am3ric@  
```

---

### 🔁 Step 2: Install & Enable Nginx

```bash
sudo yum install -y nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

Verify
```bash
sudo systemctl status nginx
```
> The nginx service is now active(running).
---

### ⚙️ Step 3: Deploy SSL Certificate

Move SSL files to a secure location:
```bash
sudo mkdir -p /etc/nginx/ssl
sudo mv /tmp/nautilus.crt /etc/nginx/ssl/
sudo mv /tmp/nautilus.key /etc/nginx/ssl/
sudo chmod 600 /etc/nginx/ssl/nautilus.*
```

then Update Nginx config:
```bash
sudo vi /etc/nginx/nginx.conf
```

Add a server block:
```nginx
server {
    listen 443 ssl;
    server_name _;

    ssl_certificate     /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    root /usr/share/nginx/html;
    index index.html;
}
```

Test config and restart:
```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

### 🔁 Step 4: Create index.html
```bash
echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html
```

---

### 🔁 Step 5: Adjust Firewall (if enabled)
```bash
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```
> firewall is disabled, disregard this part.

---

### 🔁 Step 6: Verify SSL with curl
From the jump host:
```bash
curl -Ik https://172.16.238.11/
```

expected:
```bash
HTTP/1.1 200 OK
Server: nginx/1.x.x
Content-Type: text/html
```

---

## ✅ Task Completed
- Nginx installed & running
- SSL deployed with provided cert/key
- Index page created (`Welcome!`)
- Verified via `curl` over `HTTPS`
