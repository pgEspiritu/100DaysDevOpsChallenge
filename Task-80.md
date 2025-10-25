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

### 🔁 Step 2: Verify Storage and Shared Directory

1. SSH into the Storage Server:
   ```bash
   ssh natasha@ststor01   
   ```
  
   pw: Bl@kW

2. Check the repo and doc root:
   ```bash
   cd /var/www/html
   git status
   ```

---

### 🔁 Step 3: Configure Jenkins SSH Access (If Not Yet Done)

1. On Jenkins server:
login
```bash
ssh jenkins@jenkins
```
pw: j@rv!s

generate key
```bash
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub
```
   
3. Copy the public key to each app server (tony, steve, banner):

login to each server:
Server 1:
```bash
ssh tony@stapp01
```
pw: Ir0nM@n

Server 2:
```bash
ssh steve@stapp02
```
pw: Am3ric@

Server 3:
```bash
ssh banner@stapp03
```
pw: BigGr33n

Enter this to each server:
```bash
mkdir -p ~/.ssh
cat > .ssh/authorized_keys 
```
Paste the public key then ctrl + C to end

give permission:
```bash
chmod 600 ~/.ssh/authorized_keys
```

> This ensures Jenkins can SSH into each app server to restart Apache.

For Tony:

For Steve:

For Banner:

---

### 🔁 Step 4: Install Necessary Jenkins Plugins
  
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
- Java Framework, update the java as well
  
3. Restart Jenkins after installation

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
  (since this is already a local repo on Storage Server)

  Branch to build:
  ```bash
  */master
  ```

5. Under Build Triggers, check:
  ```vbnet
  ☑ Poll SCM
  ```
  
6. In the Schedule field, enter:
  ```bash
  * * * * *
  ```
> Checks for new commits on master every minute.

7. Build Step:
Add → Execute shell
```bash
cd /var/www/html
git pull origin master
```

8. Post-build Action:
- Add → Build other projects
- Enter:
  ```bash
  manage-services
  ```
- Choose:
  ```pgsql
  Trigger only if build is stable
  ```

9. Click Save.

> build will fail since need to create manage-services job

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
