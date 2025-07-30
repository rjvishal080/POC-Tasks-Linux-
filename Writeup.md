# 🔐 Security POC Tasks

This repository demonstrates key security misconfigurations and their mitigation. Each task includes setup, exploitation, and remediation steps to help learn secure system administration practices.

---

## Task 1: 🧑‍💻 User & Permission Misconfigurations

**Description**  
Improper file permissions can allow unauthorized users to access sensitive files, leading to privilege escalation.

### ✅ Setup: Creating a Sensitive File with Weak Permissions

    # As root or sudo user
    echo "Sensitive content" > /home/studentuser/secret.txt
    chmod 777 /home/studentuser/secret.txt

### ⚠️ Exploitation: Accessing Sensitive Files

    # Any local user can now read the file
    cat /home/studentuser/secret.txt

### 🔐 Mitigation: Fixing Security Issues

    chmod 600 /home/studentuser/secret.txt
    chown studentuser:studentuser /home/studentuser/secret.txt

---

## Task 2: 🌐 Remote Access & SSH Hardening

**Description**  
Weak SSH configurations can allow unauthorized access. This task explores SSH vulnerabilities and hardening.

### ✅ Setup: Weak SSH Configuration  
Edit `/etc/ssh/sshd_config`:

    PermitRootLogin yes
    PasswordAuthentication yes

Restart SSH:

    sudo systemctl restart ssh

### 🔐 Mitigation: Secure SSH  
Edit `/etc/ssh/sshd_config`:

    PermitRootLogin no
    PasswordAuthentication no
    AllowUsers studentuser

Then:

    sudo systemctl restart ssh

---

## Task 3: 🔥 Firewall & Network Security

**Description**  
A misconfigured firewall can expose systems. Learn how to scan and secure open ports.

### ✅ Setup: Open Ports & Firewall Misconfigurations

    # Example: Open HTTP port
    sudo apt install apache2 -y
    sudo ufw allow 80

### 🔍 Scan Open Ports

    nmap -sS -Pn <target-ip>

### 🔐 Mitigation: Restrict Access

    sudo ufw deny 80
    sudo ufw enable
    sudo ufw status

---

## Task 4: ⬆️ SUID & Privilege Escalation

**Description**  
SUID binaries can be exploited for root access. Learn how to detect and fix vulnerable binaries.

### ✅ Setup: Exploitable SUID Permissions

    sudo cp /bin/bash /home/studentuser/suidbash
    sudo chmod u+s /home/studentuser/suidbash

### ⚠️ Exploitation: Escalating Privileges

    /home/studentuser/suidbash -p
    whoami  # → root

### 🔐 Mitigation: Remove Unnecessary SUID

    sudo chmod u-s /home/studentuser/suidbash

---

## Task 5: 🤖 Automated Security Auditing & Scripting

**Description**  
Automation helps detect threats in real time. This task covers writing a basic audit script.

### ✅ Setup: Writing a Security Audit Script

**`security_check.sh`**

    #!/bin/bash
    echo "Checking login attempts:"
    lastb | head

    echo "Checking running services:"
    ps aux | grep -v grep | grep -E 'apache|ssh|nginx'

### 🔐 Mitigation: Automate Security Checks

    chmod +x security_check.sh
    (crontab -l 2>/dev/null; echo "0 * * * * /home/studentuser/security_check.sh") | crontab -

---

## Task 6: 🛡️ Log Analysis & Intrusion Detection

**Description**  
Analyze logs to detect failed logins and implement countermeasures like Fail2Ban.

### ✅ Setup: Enabling System Logs

    sudo journalctl -xe
    sudo tail -f /var/log/auth.log

### 🔐 Mitigation: Use Fail2Ban

    sudo apt install fail2ban -y
    sudo systemctl enable fail2ban
    sudo systemctl start fail2ban

---
## 👨‍💻 Author
Vishal RJ 
