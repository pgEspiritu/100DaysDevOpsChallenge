# ☸️ 100 Days of DevOps – Day 83
## ✅ Task: Troubleshoot and Create Ansible Playbook

```text
An Ansible playbook needs completion on the jump host, where a team member left off. Below are the details:

1. The inventory file /home/thor/ansible/inventory requires adjustments. The playbook must run on App Server 2 in Stratos DC.
Update the inventory accordingly.

2. Create a playbook /home/thor/ansible/playbook.yml. Include a task to create an empty file /tmp/file.txt on App Server 2.

Note: Validation will run the playbook using the command ansible-playbook -i inventory playbook.yml.
Ensure the playbook works without any additional arguments.
```

---

📝 Task Summary

| # | Task                  | Description                                                                |
| - | --------------------- | -------------------------------------------------------------------------- |
| 1 | Update Inventory File | Ensure App Server 2 (`stapp02`) is listed with proper connection variables |
| 2 | Create Playbook       | Write `/home/thor/ansible/playbook.yml` with a file creation task          |
| 3 | Run Validation        | Execute using `ansible-playbook -i inventory playbook.yml`                 |
| 4 | Verify Result         | Confirm `/tmp/file.txt` exists on App Server 2                             |

---

### 🔁 Step 1: Navigate to the Ansible Directory

From jumphost
```bash
cd /home/thor/ansible
```

---

### 🔁 Step 2: Check Inventory File

1. Open the inventory file:
```bash
vi /home/thor/ansible/inventory
```

content:
```ini
stapp02 ansible_host=172.238.16.204 ansible_user=steve ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

Error found: 
- Wrong IP Address it should be `172.16.238.11`
- No password for ssh

2. Update the entry for App Server 2::
```ini
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3rica ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

3. Save and exit the editor.

4. Verify syntax:
```bash
ansible-inventory -i inventory --list
```
> stapp02 under app_servers with correct ip address

---

### 🔁 Step 3: Create Ansible Playbook

1. Create the playbook.yml file:
```bash
vi /home/thor/ansible/playbook.yml
```

2. Add the following content:
```yaml
---
- name: Create a file on App Server 2
  hosts: stapp02
  become: true
  tasks:
    - name: Create an empty file /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```

---

### 🔁 Step 4: Run the Playbook

Execute the playbook
```bash
ansible-playbook -i inventory playbook.yml
```

---

### 🔁 Step 5: Verify File Creation on App Server 2

Manually check via SSH:
```bash
ssh steve@stapp02
```
pw: Am3ric@

```bash
ls -l /tmp/file.txt
```

output:
```bash
-rw-r--r-- 1 root root 0 Nov 4 14:14 /tmp/file.txt
```

---

## 🎯 Task Completed
| ✅  | Item                                                    |
| -- | ------------------------------------------------------- |
| ✔️ | Updated `/home/thor/ansible/inventory` for App Server 2 |
| ✔️ | Created `/home/thor/ansible/playbook.yml` playbook      |
| ✔️ | Verified successful Ansible execution                   |
| ✔️ | Confirmed `/tmp/file.txt` created on `stapp02`          |
| ✔️ | Playbook runs successfully using provided command       |



