# 🧪 100 Days of DevOps – Day 3  
## ✅ Task 3: Disable Direct SSH Root Login on All App Servers

---

### 📝 Task Description

Your task is to **disable direct SSH root login** on **all App Servers** within the Stratos Datacenter:
- `stapp01`
- `stapp02`
- `stapp03`

This is a common security practice to prevent unauthorized root access via SSH.

---

### 🔁 Step 1: SSH into Each App Server One by One

From the jump host, connect to each app server:

```bash
ssh tony@stapp01     # App Server 1
ssh steve@stapp02    # App Server 2
ssh banner@stapp03   # App Server 3
```

> Use the credentials provided in the KodeKloud environment. Confirm with yes if prompted.

---

### 🛠️ Step 2: Edit the SSH Configuration File
On each server, open the SSH config file:

```bash
sudo vi /etc/ssh/sshd_config
```

Locate the line:

```bash
#PermitRootLogin yes
```

Change it to:

```bash
#PermitRootLogin no
```

> 📝 If the line is commented `(#)`, remove the `#`.
> If the line doesn’t exist, add it manually at the end of the file.

---

### 💾 Step 3: Save and Exit
In `vi`, press:
- `Esc`
- Type `:wq`
- Press `Enter`

---

### 🔄 Step 4: Restart the SSH Service
Run the following command to apply changes:

```bash
sudo systemctl restart sshd
```

> ✅ Repeat Steps 2–4 on all three app servers.

---

### 🔍 Step 5: Verify the Change (Optional)
To confirm the change:

```bash
sudo grep PermitRootLogin /etc/ssh/sshd_config
```

Expected output:

```bash
PermitRootLogin no
```
---

## ✅ Task Complete!

Configuring Each Server:
Server 1:
![Server 1](images/Day3.1)

Server 2:
![Server 2](images/Day3.2)

Server 3:
![Server 3](images/Day3.3)
