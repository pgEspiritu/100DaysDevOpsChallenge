# 🧪 100 Days of DevOps – Day 25
## ✅ Task: Git Merge Branches

```text
The Nautilus application development team has been working on a project repository /opt/ecommerce.git.
This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC.
They recently shared the following requirements with DevOps team:

Create a new branch datacenter in /usr/src/kodekloudrepos/ecommerce repo from master
and copy the /tmp/index.html file (present on storage server itself) into the repo.
Further, add/commit this file in the new branch and merge back that branch into master branch.
Finally, push the changes to the origin for both of the branches.
```

---

Task
- Create a new branch `datacenter` from `master` in /usr/src/kodekloudrepos/ecommerce.
- Copy /tmp/index.html into the repo.
- Add & commit this file in `datacenter` branch.
- Merge `datacenter` branch into `master`.
- Push changes to origin for both branches.


---

### 🔁 Step 1: SSH into Storage Server
```bash
ssh natasha@ststor01
```

password
```bash
Bl@kW
```

---

### 🔁 Step 2: Go to Repo
```bash
cd /usr/src/kodekloudrepos/ecommerce
```

Ensure you are on master
```bash
sudo git checkout master
```
> Output: `Already on 'master`

---

### 🔁 Step 3: Create and switch to new branch datacenter
```bash
sudo git checkout -b datacenter
```

---

### 🔁 Step 4: Copy file into repo
```bash
sudo cp /tmp/index.html .
```

---

### 🔁 Step 5: Stage & commit file
```
sudo git add index.html
sudo git commit -m "Add index.html to datacenter branch"
```

---

### 🔁 Step 6: Switch back to master and merge
```bash
sudo git checkout master
sudo git merge datacenter
```

---

### 🔁 Step 7: Push both branches to origin
```bash
sudo git push origin master
sudo git push origin datacenter
```

---

### 🔁 Step 8: Verification
Check branch list:
```bash
git branch -a
```

Check commit log:
```bash
git log --oneline --graph --all
```

Check remote branches:
```bash
git ls-remote --heads origin
```

Expected:
- Both master and datacenter exist on remote.
- index.html appears in repo and is tracked in master.

![Task 25 - Git Merge Branches.1](images_4/Day-25.1.PNG)
![Task 25 - Git Create Branches.2](images_4/Day-25.1.PNG)

---


## ✅ Task Completed
- New branch datacenter created successfully from master.
- File index.html copied, staged, and committed in datacenter.
- Branch datacenter merged back into master without conflicts.
- Both branches (master and datacenter) pushed to origin.
- Verified with git branch -a, git log --oneline --graph --all, and git ls-remote --heads origin.
