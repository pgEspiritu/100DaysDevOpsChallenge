# 🧪 100 Days of DevOps – Day 12

## ✅ Task: Fix Apache Service on Port 5002

```text
Our monitoring tool has reported an issue in Stratos Datacenter.
One of our app servers has an issue, as its Apache service is not reachable on port 5002 (which is the Apache port).
The service itself could be down, the firewall could be at fault, or something else could be causing the issue.

Use tools like telnet, netstat, etc. to find and fix the issue.
Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command curl http://stapp01:5002 command from jump host.
```

---

### 📝 Task Description

On App Server 1 (stapp01):
- Verify Apache service status.
- Check if it is listening on port 3002.
- Inspect firewall rules.
- Fix the issue (service, config, firewall).
- Confirm access from Jump Host with curl.

---

### 🔁 Step 1: SSH into App Server 1
From the jump host, connect to App Server 1:
```bash
ssh tony@stapp01
```
- tony – username
- stapp01 – hostname of App Server 1

Enter the password when prompted:
```bash
Ir0nM@n
```

---

### 📦 Step 2: Check Apache Service Status
```bash
sudo systemctl status httpd
```
#### Description

| Part | Description |
|------|-------------|
| `sudo` | Runs the command with superuser privileges, required to check service status on protected system services. |
| `systemctl` | The command-line utility to control the `systemd` system and service manager. |
| `status httpd` | Displays the current status of the Apache HTTP Server (`httpd`), including whether it is running, stopped, or has errors. |

> This command checks and displays detailed information about the Apache HTTP Server (httpd) service, such as its active state, process ID, uptime, and recent log messages.


- If service is inactive, start it:
```bash
sudo systemctl start httpd
```
#### Description

| Part          | Description                                                                     |
| ------------- | ------------------------------------------------------------------------------- |
| `sudo`        | Runs the command with superuser privileges, required to manage system services. |
| `systemctl`   | The command-line utility to control the `systemd` system and service manager.   |
| `start httpd` | Starts the Apache HTTP Server (`httpd`) service immediately.                    |

> This command starts the Apache HTTP Server (httpd) service, making the web server available to handle incoming HTTP requests.

---

### Error Encountered
```bash
Job for httpd.service failed because the control process exited with error code.
See "systemctl status httpd.service" and "journalctl -xe" for details.
```

### Fix it

#### Step 1: Check Apache service status
```bash
sudo systemctl status httpd -l
```
> Purpose: Shows why httpd failed to start (error messages, misconfigurations, missing ports).

#### Step 2: Check Apache error logs
```bash
sudo journalctl -xeu httpd
```

#### Description
| Part         | Description                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------ |
| `sudo`       | Runs the command with superuser privileges, required to view system logs for protected services. |
| `journalctl` | Views logs managed by `systemd`'s journal.                                                       |
| `-x`         | Shows **detailed explanations** for log messages when available.                                 |
| `-e`         | Jumps directly to the **end of the log output** (most recent entries).                           |
| `-u httpd`   | Filters the logs to only show entries related to the **Apache HTTP Server (`httpd`)** service.   |

> Purpose: Displays detailed logs for the service.

#### Step 3: Verify Apache configuration syntax
```bash
sudo apachectl configtest
```

Output:
```text
Syntax Ok
```

#### Description
| Part         | Description                                                                                            |
| ------------ | ------------------------------------------------------------------------------------------------------ |
| `sudo`       | Runs the command with superuser privileges, required to check configuration files of a system service. |
| `apachectl`  | The command-line tool to control the Apache HTTP Server (`httpd`).                                     |
| `configtest` | Tests the Apache configuration files for **syntax errors** without starting or restarting the server.  |


**Purpose:**
- Checks if there are misconfigurations in httpd.conf or virtual hosts.
- If you see Syntax OK, the config file is fine.

#### Step 4: Check if something is already using port 5002
```bash
sudo netstat -tulnp | grep 5002
```

#### Description
| Part      | Description                                                                                               |                                                                                          |
| --------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `sudo`    | Runs the command with superuser privileges, required to view details about processes using network ports. |                                                                                          |
| `netstat` | Displays network connections, routing tables, interface statistics, and listening ports.                  |                                                                                          |
| `-t`      | Shows **TCP** connections.                                                                                |                                                                                          |
| `-u`      | Shows **UDP** connections.                                                                                |                                                                                          |
| `-l`      | Displays only **listening** sockets (services waiting for connections).                                   |                                                                                          |
| `-n`      | Shows **numeric addresses and port numbers** instead of resolving names.                                  |                                                                                          |
| `-p`      | Shows the **PID and program name** of the process using the port.                                         |                                                                                          |
| \`        | grep 3002\`                                                                                               | Filters the output to only show lines containing `3002` (the port number being checked). |

**Purpose:**
This command checks whether any process is listening on port 5002, displaying details such as the protocol, IP address, PID, and program name of the service using that port.

#### Output
The command showed:
```bash
tcp        0      0 127.0.0.1:5002          0.0.0.0:*               LISTEN      430/sendmail: accep 
```
> This shows that port 5002 is already bound by sendmail, not Apache (httpd). That’s why httpd failed to start — it couldn’t bind to the same port.

#### Step 5: Stop sendmail service
```bash
sudo systemctl stop sendmail
sudo systemctl disable sendmail
```
> prevent sendmail from starting again

---

### Issue already been fix. Try to start Apache Again
### 📦 Step 3: Start and Enable Apache Service Status
```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

### 🔍 Step 4: Verify Apache is Listening on Port 5002
```bash
sudo netstat -tulnp | grep 5002
```
Output:
```bash
tcp        0      0 0.0.0.0:3002            0.0.0.0:*               LISTEN      2173/httpd
```
>  This shows that port 3002 is now bound by httpd/Apache.

---

### 🔥 Step 5:  Verify Apache config
Check ports file or main Apache config:
```bash
sudo grep -i listen /etc/httpd/conf/httpd.conf
sudo grep -i listen /etc/httpd/conf.d/*.conf
```
> it listen to 5002

Then restart Apache:
```bash
sudo systemctl restart httpd
```

---

### 🔍 Step 6: Allow port 5002 through firewall using iptables

```bash
sudo iptables-legacy -L -n | grep 5002
```
output
```bash
sudo: iptables-legacy: command not found
```
> means that your system doesn’t have iptables-legacy installed

Since iptables-legacy is not installed, use the default iptables:

```bash
sudo iptables -I INPUT -p tcp --dport 5002 -j ACCEPT
sudo service iptables save
```

#### Description
| Part                    | Description                                                                      |
| ----------------------- | -------------------------------------------------------------------------------- |
| `sudo`                  | Runs the command with superuser privileges, required to modify firewall rules.   |
| `iptables`              | Command-line tool to configure Linux kernel firewall rules.                      |
| `-I INPUT`              | Inserts a rule into the **INPUT chain**, which handles incoming network traffic. |
| `-p tcp`                | Specifies the **protocol** as TCP.                                               |
| `--dport 5002`          | Specifies the **destination port** to allow, in this case, port `5002`.          |
| `-j ACCEPT`             | The **target action** for matching packets: accept them.                         |
| `service iptables save` | Saves the current iptables rules so that they persist after a system reboot.     |

**Purpose:**
The first command allows incoming TCP traffic on port 5002 by inserting a rule into the firewall. The second command saves the updated firewall rules to ensure they remain active after reboot.

---

### 🔥 Step 7: Test connectivity
From stapp01:
```bash
curl http://localhost:5002
```
From jump host:
```bash
curl http://stapp01:5002
```
> It both replied Apache Test Page in HTLM, it means the task is now resolved.

---

## ✅ Task Complete!
