# 🧪 100 Days of DevOps – Day 1  

## ✅ Task 1: Linux User Setup with Non-Interactive Shell

---

### 📝 Task Description

Create a user named `john` with a **non-interactive shell** on **App Server 1**.

---

### 🔁 Step 1: SSH into App Server 1
From the jump host, connect to **App Server 1** using SSH:

```bash
ssh tony@stapp01
```

- `tony` – username
- `stapp01` – hostname of App Server 1

> 📝 If prompted with "Are you sure you want to continue connecting (yes/no)?", type `yes` and press Enter.

---

### 🔐 Step 2: Enter the Password

When prompted, type the password below and press Enter:

```css
Ir0nM@n
```

> ⚠️ The password is invisible while typing — just type it and hit Enter.

You’ll know you’re connected when you see a prompt like:

```ruby
[tony@stapp01 ~]$
```

---

### 👤 Step 3: Create the User with a Non-Interactive Shell
Once logged in to App Server 1, run the following command:

```bash
sudo useradd -s /sbin/nologin john
```
> Optional: To view all available login shells, you can run:

```bash
cat /etc/shells
```

---

### 🔍 Step 4: Verify User Creation
Run this command to verify the user `john` was created:

```bash
getent passwd john
```

You should see an output similar to:

```ruby
john:x:1001:1001::/home/john:/sbin/nologin
```

---

## ✅ Task Complete!

![Task 1 - Linux User Setup with Non-Interactive Shell](./Task%201%20-%20Linux%20User%20Setup%20with%20Non-Interactive%20Shell.png)

You can now type `exit` to disconnect from App Server 1 and return to the jump host.


