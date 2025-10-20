# ☸️ 100 Days of DevOps – Day 76
## ✅ Task: Jenkins Project Security

```text
The xFusionCorp Industries has recruited some new developers. There are already some existing jobs on Jenkins and
two of these new developers need permissions to access those jobs. The development team has already shared those
requirements with the DevOps team, so as per details mentioned below grant required permissions to the developers.

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

1. There is an existing Jenkins job named Packages, there are also two existing Jenkins users named sam with
password sam@pass12345 and rohan with password rohan@pass12345.


2. Grant permissions to these users to access Packages job as per details mentioned below:

    a.) Make sure to select Inherit permissions from parent ACL under inheritance strategy for granting
    permissions to these users.
    b.) Grant mentioned permissions to sam user : build, configure and read.
    c.) Grant mentioned permissions to rohan user : build, cancel, configure, read, update and tag.

Note:

1. Please do not modify/alter any other existing job configuration.
2. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on
Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre.
Also Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
3. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us
for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to
record and share your work.
```

---

📝 Task Summary

| Step | Action                                                                                                  |
| ---- | ------------------------------------------------------------------------------------------------------- |
| 1    | Access Jenkins UI using admin credentials                                                               |
| 2    | Locate the existing Jenkins job named `Packages`                                                        |
| 3    | Enable project-based security for that job                                                              |
| 4    | Configure inheritance strategy to “Inherit permissions from parent ACL”                                 |
| 5    | Grant user `sam` permissions: **Build**, **Configure**, and **Read**                                    |
| 6    | Grant user `rohan` permissions: **Build**, **Cancel**, **Configure**, **Read**, **Update**, and **Tag** |
| 7    | Save changes and verify access                                                                          |

---

### 🔁 Step 1: Access Jenkins Dashboard

1. Click the Jenkins button on the top bar of the Stratos environment.
2. Login credentials:
  - Username: admin
  - Password: Adm!n321

---

### 🔁 Step 2: Enable Global Matrix-Based Authorization
1. From Jenkins dashboard, go to:
   ```pgsql
   Manage Jenkins → Security
   ```
2. Under Authorization, select:
   ```bash
   Matrix-based security
   ```
3. Add users:
 - admin → select all permissions
 - sam → grant only Overall → Read
 - rohan → grant only Overall → Read
  > ✅ This ensures both users can log in but have limited global access.
4. Click Save.

---

### 🔁 Step 3: Enable Project-Based Security for the Job
1. Go to Jenkins Dashboard → Packages job → Configure.
2. Scroll down to Enable project-based security and check the box.
3. Under Inheritance Strategy, select:
   ```pgsql
   Inherit permissions from parent ACL
   ```
> IF not found, install all necessary plug-ins
4. Click Add user or group, then add:
   - sam
   - rohan
