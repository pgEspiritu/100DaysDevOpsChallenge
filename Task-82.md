# ☸️ 100 Days of DevOps – Day 82
## ✅ Task: Create Ansible Inventory for App Server Testing

```text
The Nautilus DevOps team is testing Ansible playbooks on various servers within their stack.
They've placed some playbooks under /home/thor/playbook/ directory on the jump host and now
intend to test them on app server 2 in Stratos DC. However, an inventory file needs creation for
Ansible to connect to the respective app. Here are the requirements:

a. Create an ini type Ansible inventory file /home/thor/playbook/inventory on jump host.
b. Include App Server 2 in this inventory along with necessary variables for proper functionality.
c. Ensure the inventory hostname corresponds to the server name as per the wiki, for example stapp01
for app server 1 in Stratos DC.

Note: Validation will execute the playbook using the command ansible-playbook -i inventory playbook.yml.
Ensure the playbook functions properly without any extra arguments.
```

---

📝 Task Summary

| # | Task                  | Description                                              |
| - | --------------------- | -------------------------------------------------------- |
| 1 | Create Inventory File | Add an `ini` type inventory under `/home/thor/playbook/` |
| 2 | Add App Server 2      | Include `stapp02` with correct SSH and Ansible variables |
| 3 | Verify Configuration  | Ensure connectivity and syntax are correct               |
| 4 | Validate              | Confirm the playbook runs using the provided inventory   |

---

### 🔁 Step 1: Access Playbook Directory

```bash
cd /home/thor/playbook
```

---

### 🔁 Step 2: Create Inventory File

1. Create the inventory file in /home/thor/playbook/:
```bash
vi /home/thor/playbook/inventory
```

2. Add the following content:
```ini
[app_servers]
stapp02 ansible_host=172.16.238.11 ansible_user=tony ansible_ssh_pass=Ir0nM@n
```

Explanation:
- stapp02 → Hostname following company convention (App Server 2)
- ansible_host → IP address of App Server 2
- ansible_user → SSH user for Ansible connections
- ansible_ssh_pass → Corresponding password for remote login

3. Save and exit the file.

---

### 🔁 Step 3: Verify Inventory Syntax

Test the syntax of your inventory:
```bash
ansible-inventory -i /home/thor/playbook/inventory --list
```
> Expected output should show stapp02 under app_servers.

---

### 🔁 Step 4: Test Connectivity (Optional Check)

You can verify Ansible connectivity to App Server 2:
```bash
ansible -i /home/thor/playbook/inventory stapp02 -m ping
```

Output:
```javascript
stapp02 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

### 🔁 Step 5: Validate Playbook Execution

Run the playbook validation command:
```bash
ansible-playbook -i /home/thor/playbook/inventory playbook.yml
```

> Ensure it executes successfully without adding extra arguments.

---

🎯 Task Completed

| ✅  | Item                                                                          |
| -- | ----------------------------------------------------------------------------- |
| ✔️ | Created `/home/thor/playbook/inventory`                                       |
| ✔️ | Added App Server 2 (`stapp02`) with required variables                        |
| ✔️ | Verified Ansible inventory syntax                                             |
| ✔️ | Confirmed Ansible connectivity to App Server 2                                |
| ✔️ | Playbook runs successfully using `ansible-playbook -i inventory playbook.yml` |

