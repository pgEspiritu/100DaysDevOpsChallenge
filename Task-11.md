# 🧪 100 Days of DevOps – Day 11

## ✅ Task: Install and Configure Tomcat Server

```text
The Nautilus application development team recently finished the beta version of one of their Java-based applications,
which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting,
they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:

a. Install tomcat server on App Server 3.
b. Configure it to run on port 8086.
c. There is a ROOT.war file on Jump host at location /tmp.

Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e curl http://stapp03:8086
````
---

### 📝 Task Description

On **App Server 3**:

1. Install **Tomcat**.
2. Configure it to run on port **8086**.
3. Deploy `ROOT.war` (found on Jump Host at `/tmp`) so the webpage works at:

```bash
curl http://stapp03:8086
```

---

### 🔁 Step 1: SSH into App Server 3

From the jump host, connect to App Server 2:
```bash
ssh banner@stapp03
```
- `banner` – username
- `stapp03` – hostname of App Server 2

Enter the password when prompted:
```bash
BigGr33n
```

![Task 11 - Install and Configure Tomcat Server.1](images2/Day-11.1.png)

---

### 📦 Step 2: Install Tomcat

Update packages and install Tomcat:
```bash
sudo yum install -y tomcat tomcat-webapps tomcat-admin-webapps tomcat-docs-webapp
```

![Task 11 - Install and Configure Tomcat Server.2](images2/Day-11.2.png)

### Description

| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, needed to install packages system-wide. |
| `yum` | The package manager used in RHEL, CentOS, and Fedora to install, update, and manage software. |
| `install` | The `yum` subcommand that installs the specified packages. |
| `-y` | Automatically answers "yes" to any prompts during installation. |
| `tomcat` | Installs the Tomcat core server. |
| `tomcat-webapps` | Installs default web applications for Tomcat (examples, demos). |
| `tomcat-admin-webapps` | Installs Tomcat Manager and Host Manager web applications for administration. |
| `tomcat-docs-webapp` | Installs Tomcat documentation accessible through the browser. |

**Purpose:**
This command installs Apache Tomcat along with its administrative tools, documentation, and example web applications in one go, without requiring confirmation prompts.

---

### ⚙️ Step 3: Configure Tomcat Port

Edit Tomcat’s server configuration file:

```bash
sudo vi /etc/tomcat/server.xml
```

### Description

| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, required because `/etc/tomcat/server.xml` is a system file. |
| `vi` | Opens the Vi text editor to view and edit files in the terminal. |
| `/etc/tomcat/server.xml` | The main configuration file for Apache Tomcat, containing settings for server ports, connectors, services, and hosts. |

**Purpose:**
This command opens Tomcat’s primary configuration file, server.xml, in the Vi editor with elevated privileges, allowing you to modify server settings such as ports, connectors, and virtual hosts.


Find this line:

```xml
<Connector port="8080" protocol="HTTP/1.1"
```

Change `8080` to `8086`:

```xml
<Connector port="8086" protocol="HTTP/1.1"
```
> Save and exit (Esc → :wq → Enter).

![Task 11 - Install and Configure Tomcat Server.3](images2/Day-11.3.png)

---

### 📂 Step 4: Create Deployment Directory

Make sure the Tomcat webapps directory exists:

```bash
sudo mkdir -p /usr/share/tomcat/webapps
```

![Task 11 - Install and Configure Tomcat Server.4](images2/Day-11.4.png)

### Description

| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, required because `/usr/share/tomcat` is a system directory. |
| `mkdir` | Creates a new directory. |
| `-p` | Creates parent directories if they don’t already exist (prevents errors if part of the path is missing). |
| `/usr/share/tomcat/webapps` | The target directory for Tomcat’s deployed web applications. |

**Purpose:**
This command ensures the `webapps` directory exists under `/usr/share/tomcat`, creating any missing parent folders. This is the location where Tomcat stores and serves deployed web applications.

---

### 📦 Step 5: Copy and Deploy ROOT.war
From the Jump Host, copy the `ROOT.war` file to App Server 2:

On Jump Host:

```bash
scp /tmp/ROOT.war banner@stapp03:/tmp/
```

![Task 11 - Install and Configure Tomcat Server.5](images2/Day-11.5.png)

### Description:

| Part | Description |
|------|-------------|
| `scp` | Securely copies files between local and remote systems over SSH. |
| `/tmp/ROOT.war` | The source file to be copied (a WAR file located in the local `/tmp` directory). |
| `steve@stapp02` | Specifies the remote username (`banner`) and remote host (`stapp03`) to which the file will be copied. |
| `:/tmp/` | The destination directory on the remote server where the file will be placed. |

**Purpose:**
This command securely copies the `ROOT.war` file from the local `/tmp` directory to the `/tmp` directory on the remote server `stapp02` using the `steve` account.

On App Server 2:

remove first old files:
```bash
sudo rm -rf /usr/share/tomcat/webapps/ROOT
```

### Description

| Part                             | Description                                                                                 |
| -------------------------------- | ------------------------------------------------------------------------------------------- |
| `sudo`                           | Runs the command with superuser privileges, required to delete files in system directories. |
| `rm`                             | Removes files or directories.                                                               |
| `-r`                             | Recursively deletes the directory and its contents.                                         |
| `-f`                             | Forces deletion without prompting for confirmation.                                         |
| `/usr/share/tomcat/webapps/ROOT` | The target directory for removal, which is the extracted Tomcat ROOT application.           |

**purpose:**
This command forcefully deletes the Tomcat ROOT application directory and all its contents, commonly used when redeploying a new version of the application.

```bash
sudo mv /tmp/ROOT.war /usr/share/tomcat/webapps/
```

### Description

| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, required because `/usr/share/tomcat/webapps` is a system directory. |
| `mv` | Moves or renames files and directories. |
| `/tmp/ROOT.war` | The source file to be moved (WAR file currently in the `/tmp` directory). |
| `/usr/share/tomcat/webapps/` | The destination directory where Tomcat serves deployed applications. |

**Purpose:**
This command moves the `ROOT.war` file from the local `/tmp` directory to Tomcat’s `webapps` directory, making it available for deployment.

check if file is extracted
```bash
ls -l /usr/share/tomcat/webapps/
```
> You should see both ROOT.war and a ROOT/ directory after Tomcat restarts.

since the output shown:
```text
total 24
-rw-r--r--  1 banner banner 4529 Aug 14 10:58 ROOT.war
drwxr-xr-x 16 root   root   4096 Aug 14 10:52 docs
drwxr-xr-x  6 root   tomcat 4096 Aug 14 10:52 host-manager
drwxr-xr-x  6 root   tomcat 4096 Aug 14 10:52 manager
```
> your ROOT.war is owned by tomcat:tomcat, then it is correct if not,
> If it is owned by banner:banner but Tomcat runs under the tomcat user. That means Tomcat might not be able to properly extract or serve the .war file, causing the “application is not setup correctly” issue.

fix it:
  a. Give the Tomcat user ownership of the .war and extracted directory:
  ```bash
  sudo chown tomcat:tomcat /usr/share/tomcat/webapps/ROOT.war
  sudo chmod 644 /usr/share/tomcat/webapps/ROOT.war
  ```
  ### Description

  | Part | Description |
  |------|-------------|
  | `sudo` | Runs the command with superuser privileges, required to modify system files. |
  | `chown tomcat:tomcat` | Sets the owner to `tomcat` and the group to `tomcat`. |
  | `/usr/share/tomcat/webapps/ROOT.war` | The WAR file for the Tomcat ROOT application. |
  | `chmod 644` | Sets the file permissions to read/write for owner, and read-only for group and others. |


  **Purpose:**
  The first command ensures the ROOT.war file is owned by the Tomcat user and group, and the second 
  command sets safe permissions so that Tomcat can read the file while preventing unauthorized modifications by other users.

![Task 11 - Install and Configure Tomcat Server.6](images2/Day-11.6.png)

---

### 📦 Step 6: Check JAVA Version
> If the ROOT.war was compiled for Java 11+ but the system uses Java 1.8, it won’t deploy correctly:

```bash
java -version
```
If needed, install a compatible JDK:

```bash
sudo yum install -y java-11-openjdk
```

![Task 11 - Install and Configure Tomcat Server.7](images2/Day-11.7.png)

### Description
| Part              | Description                                                                                    |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| `sudo`            | Runs the command with superuser privileges, required to install packages system-wide.          |
| `yum`             | The package manager used in RHEL, CentOS, and Fedora to install, update, and manage software.  |
| `install`         | The `yum` subcommand that installs the specified package.                                      |
| `-y`              | Automatically answers "yes" to any prompts during installation.                                |
| `java-11-openjdk` | Installs **Java OpenJDK 11**, an open-source implementation of the Java Development Kit (JDK). |

**Purpose:**
This command installs Java 11 on your system, which is required to run Java-based applications like Apache Tomcat.

then Restart Tomcat so it re-deploys:
```bash
sudo systemctl restart tomcat
```

---

### ▶️ Step 7: Start and Enable Tomcat

Confirm Tomcat is Installed and Running
```bash
sudo systemctl status tomcat
```

![Task 11 - Install and Configure Tomcat Server.8](images2/Day-11.8.png)

### Description
| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, required to check service status on protected system services. |
| `systemctl` | The command-line utility to control the `systemd` system and service manager. |
| `status tomcat` | Displays the current status of the Tomcat service, including whether it is running, stopped, or has errors. |

**Purpose:**
This command checks and displays detailed information about the Tomcat service, such as its active state, process ID, uptime, and recent log messages.

Start and Enable Tomcat
```bash
sudo systemctl start tomcat
sudo systemctl enable tomcat
```

![Task 11 - Install and Configure Tomcat Server.9](images2/Day-11.9.png)

### Description

| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, required to manage system services. |
| `systemctl` | The command-line utility to control the `systemd` system and service manager. |
| `start tomcat` | Starts the Tomcat service immediately. |
| `enable tomcat` | Enables the Tomcat service to start automatically on system boot. |

**Purpose:**
The first command starts the Tomcat service right away, while the second command enables it so that Tomcat automatically runs whenever the system starts.

---

### 🧪 Step 8: Test the Deployment
From App Server 2:
```bash
curl http://stapp03:8086
```

> You should see the HTML output from the deployed application.

From Jump Host:
```bash
curl http://stapp03:8086
```

---

## ✅ Task Complete!

Tomcat is now running on port `8086`, and the ROOT.war application is deployed and accessible via the base URL.

![Task 11 - Install and Configure Tomcat Server.10](images2/Day-11.10.png)
