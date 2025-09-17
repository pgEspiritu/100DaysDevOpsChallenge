# 🐳 100 Days of DevOps – Day 39
## ✅ Task: Fix and Build Dockerfile

```text
The Nautilus DevOps team is working to create new images per requirements shared by the development team.
One of the team members is working to create a Dockerfile on App Server 1 in Stratos DC.
While working on it she ran into issues in which the docker build is failing and displaying errors.
Look into the issue and fix it to build an image as per details mentioned below:

a. The Dockerfile is placed on App Server 1 under /opt/docker directory.
b. Fix the issues with this file and make sure it is able to build the image.
c. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html.

Note: Please note that once you click on FINISH button all existing images, the containers will be destroyed and new image will be built from your Dockerfile.
```

---

📝 Task List
- SSH into App Server 1
- Inspect Dockerfile under /opt/docker
- Fix invalid paths and COPY sources
- Build Docker image from the Dockerfile
- Verify build succeeds


---

🔁 Step 1: SSH into App Server 1

### 🔁 Step 1: SSH into App Server 1
```bash
ssh tony@stapp01
```

password
```bash
Ir0nM@n
```

then login as super user
```bash
sudo su
```

---

### 🔁 Step 2: Inspect Dockerfile

Navigate to /opt/docker
```bash
cd /opt/docker
```

verify if Dockerfile is present in the directory
```bash
ls
```
> Dockerfile is present

check the Dockerfile
```Bash
cat Dockerfile
```

Output:
```nginx
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf
RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf
RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf
COPY /server.crt /usr/local/apache2/conf/server.crt
COPY /server.key /usr/local/apache2/conf/server.key
COPY ./index.html /usr/local/apache2/htdocs/
```

Issues Found:
- Wrong config path used in `sed` commands
    - You used `conf/httpd.conf`, but inside the `httpd:2.4.43` image the correct path is `/usr/local/apache2/conf/httpd.conf`.
- COPY source paths don’t exist in build context
  - `COPY /server.crt` and `COPY /server.key` → these look for files at the root of the build context (/opt/docker), but your certs are actually under /opt/docker/certs/.
  - `COPY ./index.html` → this looks for `index.html` directly under `/opt/docker`, but in the file it is `/opt/docker/html/index.html`.
- Missing filenames on last COPY
  - COPY `./index.html /usr/local/apache2/htdocs/` works, but it’s better to specify the full target filename `(/usr/local/apache2/htdocs/index.html)` to avoid issues.
- Redundant RUN instructions
  - Multiple `RUN sed -i ...` could be chained into one layer (not strictly an error, but inefficient).

---

### 🔁 Step 3: Fix Dockerfile

Edit file:
```bash
vi Dockerfile
```

Use this Correct Version:
```dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf \
 && sed -i '/LoadModule ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf \
 && sed -i '/LoadModule socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf \
 && sed -i '/Include conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt
COPY certs/server.key /usr/local/apache2/conf/server.key
COPY html/index.html /usr/local/apache2/htdocs/index.html
```

save and exit

---

### 🔁 Step 4: Build the Image

Ensure first files are in place:
```bash
ls -l /opt/docker
```

the run container:
```bash
docker build -t naut-httpd .
```

> error encountered: => ERROR [internal] load metadata for docker.io/library/httpd:2.4.43              61.0s

Manually pull the httpd:2.4.43
```bash
docker pull httpd:2.4.43
```

then build again:
```bash
docker build -t naut-httpd .
```

---

### 🔁 Step 5: Verify Build

Run container:
```bash
docker run -d --name test-httpd -p 8080:8080 naut-httpd
```

Check response:
```bash
curl -I http://localhost:8080
```

Output:
```nginx
HTTP/1.1 200 OK
Date: Wed, 17 Sep 2025 03:26:06 GMT
Server: Apache/2.4.43 (Unix) OpenSSL/1.1.1d
Last-Modified: Wed, 17 Sep 2025 02:38:15 GMT
ETag: "16-63ef620e4b3c0"
Accept-Ranges: bytes
Content-Length: 22
Content-Type: text/html
```

---

## ✅ Task Completed
- Fixed Dockerfile under /opt/docker on App Server 1.
- Built image successfully (naut-httpd).
- Verified Apache runs on port 8080 with SSL enabled and correct index page.
