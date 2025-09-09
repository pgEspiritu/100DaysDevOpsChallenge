# 🐳 100 Days of DevOps – Day 35
## ✅ Task: Install Docker Packages and Start Docker Service

```text
The Nautilus DevOps team aims to containerize various applications following a recent meeting with the application development team.
They intend to conduct testing with the following steps:

1. Install docker-ce and docker compose packages on App Server 3.
2. Initiate the docker service.
```

---

Tasks
-  SSH into App Server 3
-  Update system packages and prerequisites
-   Install Docker CE
-   Install Docker Compose
-   Enable and start Docker service

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

### 🔁 Step 2: Update Packages
```bash
sudo yum update -y
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

---

### 🔁 Step 3: Install Docker CE

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

---

### 🔁 Step 4: Install Docker Compose

```bash
sudo yum update
sudo yum install docker-compose-plugin
```

Verify:
```bash
docker compose version
```

---

### 🔁 Step 5: Enable & Start Docker Service

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

---

✅ Task Completed
- Installed docker-ce and docker-compose on App Server 3.
- Enabled and started the docker service.
