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

| # | Task                              | Description                                        |
| - | --------------------------------- | -------------------------------------------------- |
| 1 | Create Deployment Job             | `nautilus-app-deployment` (upstream)               |
| 2 | Create Service Management Job     | `manage-services` (downstream)                     |
| 3 | Configure Chained Build           | Trigger `manage-services` only on successful build |
| 4 | Restart Apache on All App Servers | Restart `httpd` after deployment success           |
| 5 | Validate End-to-End Chain         | Push update → auto deploy → auto restart Apache    |

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
- Java Framework, update the java as well
  
3. Restart Jenkins after installation

---

### 🔁 Step 3: Create SSH Credentials for Storage and App Server

1. In Jenkins, go to:
```go
Manage Jenkins → Credentials → System → Global credentials (unrestricted)
```

2. Click Add Credentials and enter:

For Sarah
```nginx
Kind: Username with password
Scope: Global
Username: sarah
Password: Sarah_pass123
ID: ststor01-ssh
Description: SSH credentials for Storage Server (ststor01)
```

For Tony
```nginx
Kind: Username with password
Scope: Global
Username: tony
Password: Ir0nM@n
ID: stapp01-ssh
Description: SSH credentials for App Server 1 (stapp01)
```

For Steve
```nginx
Kind: Username with password
Scope: Global
Username: steve
Password: Am3ric@
ID: stapp02-ssh
Description: SSH credentials for App Server 2 (stapp02)
```

For Banner
```nginx
Kind: Username with password
Scope: Global
Username: banner
Password: BigGr33n
ID: stapp03-ssh
Description: SSH credentials for App Server 3 (stapp03)
```

3. Click Create ✅

---

### 🔁 Step 4: Create Remote SSH for Storage and App Server

1. In Jenkins, go to:
```go
Manage Jenkins → System
```

2. Under SSH remote hosts, add each server (sarah, tony, steve, banner)

For sarah
```bash
Hostname: ststor01
Port: 22
Credentials: sarah
☑ pty
```
Check connection.
> Can't connect to server

To resolve:
  1. Login as sarah
  2. Issue found: login failed, password incorrect
  3. Try to reset sarah password via Storage Server login: Natasha
     ```bash
     sudo sarah
     ```
     > error: user sarah does not exist or the user entry does not contain all the required fields
     It means that we need to create user credential for sarah
  4. Create sarah
     ```bash
     sudo useradd sarah  # create user sarah
     sudo passwd sarah  # create password
     ```
     then enter
     ```bash
     Sarah_pass123
     ```
  5. Verify by log-in as sarah with new password
     > successfully ✅

  6. Check connection again
     > successful connection ✅


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

3. Under Public over ssh add storage server:
   - SSH Server -> Click Add
   - enter the following:
     ```bash
     Name: ststor01
     Hostname: ststor01
     Username: natasha
     Remote Directory: /data
     ☑ Use Password authentication, or use different key
     Password: Bl@kW
     ```
   - Test Configuration
     > Issue found: `jenkins.plugins.publish_over.BapPublisherException: Failed to connect and initialize SSH connection. Message: [Failed to change to remote directory [/data]]`

     Fix:
     1. log-in as natasha
        ```bash
        ssh natasha@ststor01
        ```
        pw: Bl@kW
     2. Check if the data folder is present
        ```bash
        ls -ld /data
        ```
        > No such file or directory, therefore create a data folder
     3. Make a data directory
        ```bash
        sudo mkdir -p /data
        ```
     4. Give permission to file:
        ```bash
        sudo chown natasha:natasha /data
        sudo chmod 755 /data
        ```

---

### 🔁 Step 5: Create Upstream Job – nautilus-app-deployment

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

5. Under Build Triggers, check:
   ```vbnet
   ☑ Trigger build remotely
   ```

   Authentication Token:
   ```bash
   KODEKLOUDJENKINS
   ```
   > Take note of the URL to trigger remotely

6. Under Environment
   ```vbnet
   ☑ Send files or execute commands over SSH after the build runs
   ```

   Then input the following:
   ```bash
   Name: Ststor01 #Already available
   Source files: **/*
   ```
   
7. Apply and Save

8. Build Now
   > successful build ✅

---

### 🔁 Step 6: Configure Webhook for Repo

1. Login in Gitea using Sarah
2. In Gitea, 
   ```go
   sarah/web - Settings - webhooks - add webhook - gitea
   ```
3. Copy URL from Jenkins Build Trigger
   Target URL
   ```bash
   https://8080-port-i5dhijsgjsc5wngx.labs.kodekloud.com/job/nautilus-app-deployment/build?token=KODEKLOUDJENKINS
   ```
   > it came from this: Use the following URL to trigger build remotely: JENKINS_URL/job/nautilus-app-deployment/build?token=TOKEN_NAME or /buildWithParameters?token=TOKEN_NAME

4. Add webhook

5. Open the created webhook, then test delivery
6. 


---

### 🔁 Step 6: Create Downstream Jenkins Job – manage-services
1. From Jenkins Dashboard → New Item → Name:
   ```go
   manage-services
   ```

  Select Freestyle project → OK.

2. Build Step:
Add → Execute shell
```bash
for server in tony steve banner
do
  echo "Restarting Apache on $server..."
  ssh $server "sudo systemctl restart httpd"
  ssh $server "sudo systemctl status httpd | grep Active"
done
```

3. Click Save.

---

### 🔁 Step 7: Test the Chained Build

1. SSH as Sarah:
```bash
ssh sarah@ststor01
cd ~/web
echo "Welcome to xFusionCorp Chained Build!" > index.html
git commit -am "Updated index.html for chained build test"
git push origin master
```
