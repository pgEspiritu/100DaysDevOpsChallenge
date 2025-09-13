# 🧪 100 Days of DevOps – Day 41
## ✅ Task: Create Custom Dockerfile with Apache on Custom Port

```text
As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects.
Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file /opt/docker/Dockerfile
(please keep D capital of Dockerfile) on App server 2 in Stratos DC and configure to build an image with the following requirements:

a. Use ubuntu:24.04 as the base image.
b. Install apache2 and configure it to work on 5002 port. (do not update any other Apache configuration settings like document root etc).
```

---

Task List
- SSH into App Server 2
- Create /opt/docker/Dockerfile
- Use ubuntu:24.04 as base image
- Install apache2 inside image
- Update Apache config to listen on 5002 instead of default 80
- Expose port 5002 in Dockerfile

---

### 🔁 Step 1: SSH into App Server 2
```bash
ssh steve@stapp02
```

password
```bash
Am3ric@
```

then login as super user
```bash
sudo su
```

---

### 🔁 Step 2: Create Directory & Dockerfile
```bash
mkdir -p /opt/docker
cd /opt/docker
vi Dockerfile
```

---

### 🔁 Step 3: Add Content to Dockerfile
Paste the following:
```dockerfile
# Base image
FROM ubuntu:24.04

# Install apache2
RUN apt-get update && apt-get install -y apache2 && apt-get clean

# Change Apache port to 5002
RUN sed -i 's/80/5002/g' /etc/apache2/ports.conf && \
    sed -i 's/*:80/*:5002/g' /etc/apache2/sites-available/000-default.conf

# Expose custom port
EXPOSE 5002

# Run apache in foreground
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

---

### 🔁 Step 4: Build Image

```bash
docker build -t custom-apache:5002 .
```

---

### 🔁 Step 5: Run Container to Test

```bash
docker run -d --name apache_test -p 5002:5002 custom-apache:5002
```

Check container:
```bash
docker ps
```

Output:
```nginx
CONTAINER ID   IMAGE                COMMAND                  CREATED          STATUS          PORTS                    NAMES
d3395b8811fe   custom-apache:5002   "/usr/sbin/apache2ct…"   31 seconds ago   Up 27 seconds   0.0.0.0:5002->5002/tcp   apache_test
```

> succesfully created image `custom-apache:5002`

---

### 🔁 Step 6: Verify Apache on Port 5002

```bash
curl -I http://localhost:5002
```

---

## ✅ Task Completed
- Dockerfile is created at /opt/docker/Dockerfile.
- Custom image built successfully.
- Apache runs on port 5002.
