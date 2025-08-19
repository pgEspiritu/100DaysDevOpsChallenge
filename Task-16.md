# 🧪 100 Days of DevOps – Day 16 

## ✅ Task: Install and Configure Nginx as an LBR

```text
Day by day traffic is increasing on one of the websites managed by the Nautilus production support team.
Therefore, the team has observed a degradation in website performance. Following discussions about this issue,
the team has decided to deploy this application on a high availability stack i.e on Nautilus infra in Stratos DC.
They started the migration last month and it is almost done, as only the LBR server configuration is pending.
Configure LBR server as per the information given below:

a. Install nginx on LBR (load balancer) server.
b. Configure load-balancing with the an http context making use of all App Servers. Ensure that you update only the main Nginx configuration file located at /etc/nginx/nginx.conf.
c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all app servers.
d. Once done, you can access the website using StaticApp button on the top bar.
```

---

tasks:  
1. Install Nginx on LBR server.
2. Configure Nginx load balancing for all app servers.
3. Ensure Apache is running on all app servers.
4. Verify the website from jump host or StaticApp button.

---

### 🔁 Step 1: SSH into LBR Server

```bash
ssh loki@stlb01
```

password
```bash
Mischi3f
```

---

### 🔁 Step 2: Install & Enable Nginx

```bash
sudo yum install -y nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

then verify
```bash
sudo systemctl status nginx
```
> now, we cab see that nginx is active

---

### ⚙️ Step 3: Configure Nginx as Load Balancer

Edit the main configuration file:
```bash
sudo vi /etc/nginx/nginx.conf
```

Inside the http { ... } block, add upstream and server configuration:
```nginx
http {
    upstream app_servers {
        server stapp01:80;
        server stapp02:80;
        server stapp03:80;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://app_servers;
        }
    }
}
```

---

### 🔁 Step 4: Test & Restart Nginx

```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

### 🔁 Step 5: Ensure Apache is Running on App Servers
SSH into each app server (`stapp01`, `stapp02`, `stapp03`) and check:
```bash
sudo systemctl status httpd
```
> All are `active (running)`
> incase it is not active, enter this:

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

### 🔁 Step 6: Verify Load Balancer
From jump host or LBR server itself:

```bash
curl http://stlb01
```

or by IP:

```bash
curl http://<LBR-IP-Adress>
```

---

## ✅ Task Completed
- Nginx installed & configured as load balancer.
- Apache verified running on all app servers.
- Load balancing confirmed with curl & StaticApp button.




---

