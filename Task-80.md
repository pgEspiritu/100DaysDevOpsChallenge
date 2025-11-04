# ☸️ 100 Days of DevOps – Day 80
## ✅ Task: Jenkins Chained Builds

```text
The DevOps team was looking for a solution where they want to restart Apache service on all app servers if the deployment goes fine
on these servers in Stratos Datacenter. After having a discussion, they came up with a solution to use Jenkins chained builds so that
they can use a downstream job for services which should only be triggered by the deployment job. So as per the requirements mentioned
below configure the required Jenkins jobs.

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and Adm!n321 password.

Similarly you can access Gitea UI on port 8090 and username and password for Git is sarah and Sarah_pass123 respectively.
Under user sarah you will find a repository named web.

Apache is already installed and configured on all app server so no changes are needed there. The doc root /var/www/html
on all these app servers is shared among the Storage server under /var/www/html directory.

1. Create a Jenkins job named nautilus-app-deployment and configure it to pull change from the master branch of web repository
on Storage server under /var/www/html directory, which is already a local git repository tracking the origin web repository.
Since /var/www/html on Storage server is a shared volume so changes should auto reflect on all apps.

2. Create another Jenkins job named manage-services and make it a downstream job for nautilus-app-deployment job.
Things to take care about this job are:

  a. This job should restart httpd service on all app servers.
  b. Trigger this job only if the upstream job i.e nautilus-app-deployment is stable.

LB server is already configured. Click on the App button on the top bar to access the app. You should be able to see the latest changes you made.
Please make sure the required content is loading on the main URL https://<LBR-URL> i.e there should not be a sub-directory like https://<LBR-URL>/web etc.


Note:


1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is
complete and no jobs are running on plugin installation/update page i.e update centre. Also some times Jenkins UI gets stuck when Jenkins
service restarts in the back end so in such case please make sure to refresh the UI page.

2. Make sure Jenkins job passes even on repetitive runs as validation may try to build the job multiple times.

3. Deployment related tasks should be done by sudo user on the destination server to avoid any permission issues so make sure to configure
your Jenkins job accordingly.

4. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review
in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.
```

---

📝 Task Summary
| # | Task                      | Description                                                |
| - | ------------------------- | ---------------------------------------------------------- |
| 1 | Setup Jenkins Credentials | For Storage and all App Servers                            |
| 2 | Create Upstream Job       | `nautilus-app-deployment` – Pull and deploy latest changes |
| 3 | Create Downstream Job     | `manage-services` – Restart Apache on all app servers      |
| 4 | Configure Chained Build   | Trigger `manage-services` only if deployment is stable     |
| 5 | Test & Verify             | Ensure successful restart and visible changes via LB URL   |

---


### 🔁 Step 1: Access Jenkins & Gitea
  
Jenkins
- URL: Click the Jenkins button on top bar
- Login:
  ```makefile
  Username: admin
  Password: Adm!n321
  ```

Gitea
- URL: Click the Gitea button on top bar
- Login:
  ```makefile
  Username: sarah
  Password: Sarah_pass123
  ```
- check if there's `web` repo

---

### 🔁 Step 2: Install Necessary Jenkins Plugins
  
1. In Jenkins, go to:
```go
Manage Jenkins → Plug-ins
```
 
2. Install the following:
- SSH
- SSH Credentials
- SSH Build Agents
- Git
- Credentials
- CloudBees
- Gitea
- Gitea check
- Publish Over SSH
  
3. Restart Jenkins after installation

---

### 🔁 Step 3: Enable Passwordless sudo in all app servers

SSH to each app server and run:
```bash
sudo visudo
```

then at the end, add this:

For stapp01:
```yaml
tony ALL=(ALL) NOPASSWD: ALL
```

For stapp02:
```yaml
steve ALL=(ALL) NOPASSWD: ALL
```

For stapp03:
```yaml
banner ALL=(ALL) NOPASSWD: ALL
```

---

### 🔁 Step 4: Create Jenkins Credentials for Storage and App Server

1. In Jenkins, go to:
```go
Manage Jenkins → Credentials → System → Global credentials (unrestricted)
```

2. Click Add Credentials and enter:

For Storage Server: Sarah
```nginx
Kind: Username with password
Scope: Global
Username: sarah
Password: Sarah_pass123
ID: sarah
```

For App Server 3: Tony
```nginx
Kind: Username with password
Scope: Global
Username: tony
Password: Ir0nM@n
ID: tony
```

For App Server 2: Steve
```nginx
Kind: Username with password
Scope: Global
Username: steve
Password: Am3ric@
ID: steve
```

For App Server 3: Banner
```nginx
Kind: Username with password
Scope: Global
Username: banner
Password: BigGr33n
ID: banner
```

For Storage Server: Natasha
```nginx
Kind: Username with password
Scope: Global
Username: natasha
Password: Bl@kW
ID: natasha
```

3. Click Create ✅

---

### 🔁 Step 5: Configure Remote SSH for Storage and App Server

1. In Jenkins, go to:
```go
Manage Jenkins → System
```

2. Under SSH remote hosts, add each server (sarah, tony, steve, banner)

For tony
```bash
Hostname: stapp01
Port: 22
Credentials: tony
☑ pty
```

For steve
```bash
Hostname: stapp02
Port: 22
Credentials: steve
☑ pty
```

For banner
```bash
Hostname: stapp03
Port: 22
Credentials: banner
☑ pty
```

> Make sure that the connection for each is successful

3. Under Public over ssh add storage server:
   - SSH Server -> Click Add
   - enter the following:
     ```bash
     Name: ststor01
     Hostname: ststor01
     Username: natasha
     Remote Directory: /var/www/html
     ☑ Use Password authentication, or use different key
     Password: Bl@kW
     ```
   - Test Configuration: "Successful"
   
---

### 🔁 Step 6: Create Upstream Job – nautilus-app-deployment

1. From Jenkins Dashboard → New Item
2. Enter name:
```bash
nautilus-app-deployment
```
3. Select Freestyle project → OK

4. Under Source Code Management
Git Repository URL:
```bash
http://git.stratos.xfusioncorp.com/sarah/web.git
```

Credential: sarah

Branch to build:
```bash
*/master
```

5. Under Build Environment
- ✓ Send files or execute commands over SSH after the build runs
- SSH Server: `ststor01`
- Transfer Set:
  - Source files: **/*
> Leave other options empty
   
6. Post-build action
- Click Add post-build action → Build other projects
- Projects to build: manage-services
- ✓ Check: "Trigger only if build is stable"

7. Apply and save
   
---

### 🔁 Step 7: Create manage-services Job

1. From Jenkins Dashboard → New Item
2. Enter name:
```bash
manage-services
```
3. Select Freestyle project → OK

4. Build Trigger:
- ✓ Check: "Build after other projects are built"
- Projects to watch: nautilus-app-deployment
- ✓ Check: "Trigger only if build is stable"

5. Build Steps:
Add 3 separate "Execute shell script on remote host using SSH":
- SSH Site 1: tony@stapp01:22
- SSH Site 2: steve@stapp02:22
- SSH Site 3: banner@stapp03:22

In each command, enter this:
```
sudo systemctl restart httpd && sudo systemctl status httpd --no-pager
```

6. Click Save

---

### 🔁 Step 8: Test the Pipeline

- Build nautilus-app-deployment manually
- Verify manage-services triggers automatically
- Check app servers serve content via load balancer

---

## 🎯 Task Completed

| ✅  | Item                                              |
| -- | ------------------------------------------------- |
| ✔️ | Jenkins credentials configured for all servers    |
| ✔️ | SSH connections verified successfully             |
| ✔️ | `nautilus-app-deployment` (upstream) job created  |
| ✔️ | `manage-services` (downstream) job configured     |
| ✔️ | Apache restarted automatically on all app servers |
| ✔️ | Jenkins chained build trigger works as expected   |
| ✔️ | Verified app content via Load Balancer URL        |

