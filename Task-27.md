# 🧪 100 Days of DevOps – Day 27
## ✅ Task: Git Revert Some Changes

```text
The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/beta
present on Storage server in Stratos DC. However, they reported an issue with the recent commits being pushed to this repo.
They have asked the DevOps team to revert repo HEAD to last commit. Below are more details about the task:
1, In /usr/src/kodekloudrepos/beta git repository, revert the latest commit ( HEAD ) to the previous commit
(JFYI the previous commit hash should be with initial commit message ).
2. Use revert beta message (please use all small letters for commit message) for the new revert commit.
```

---

Task
- Navigate to `/usr/src/kodekloudrepos/beta`
- Check commit history to confirm the last and initial commit
- Revert the latest commit (HEAD) back to previous
- Ensure commit message is exactly revert beta
- Verify repo state

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
cd /usr/src/kodekloudrepos/beta
```

---

### 🔁 Step 3: Check Commit History
```bash
sudo git log --oneline
```

Expected Output:
```sql
a3f5c12  Added feature X
7c9d0b1  initial commit
```

---

### 🔁 Step 4: Revert Latest Commit
```bash
sudo git revert --no-edit HEAD
```
But since the task requires a specific commit message:
```bash
sudo git revert HEAD -m "revert beta"
```

⚠️ Note: -m is used for merge commits; for a normal commit, just use -m with message:
```bash
sudo git revert HEAD --no-edit
sudo git commit --amend -m "revert beta"
```

---

### 🔁 Step 5: Verify
```bash
sudo git log --oneline
```

Expected latest entry:
```sql
c8f1e23  revert beta
a3f5c12  Added feature X   (reverted)
7c9d0b1  initial commit
```

---

## ✅ Task Completed
- Navigated to `/usr/src/kodekloudrepos/beta`.
- Verified commit history with `git log`.
- Reverted the latest commit (HEAD) to previous commit.
- Added new commit with message "revert beta".
- Confirmed with `git log --oneline`.
