# Security Proof of Concept (POC) Tasks

This repository documents a series of security proof-of-concept tasks demonstrating common misconfigurations and their remediations. These exercises are intended for educational use in controlled environments only.

---

## Task 1: User and Permission Misconfigurations

**Objective**  
Demonstrate the risks of improper file permissions and how to secure sensitive files.

### Setup

Create a sensitive file with overly permissive permissions:

    echo "Sensitive content" > /home/studentuser/secret.txt
    chmod 777 /home/studentuser/secret.txt

### Exploitation

Any local user can read the file:

    cat /home/studentuser/secret.txt

### Mitigation

Restrict file access:

    chmod 600 /home/studentuser/secret.txt
    chown studentuser:studentuser /home/studentuser/secret.txt

---

## Task 2: SSH Configuration and Hardening

**Objective**  
Show how weak SSH settings can expose a system and demonstrate best practices for secure configuration.

### Setup

Edit `/etc/ssh/sshd_config` to enable insecure settings:

    PermitRootLogin yes
    PasswordAuthentication yes

Restart the SSH service:

    sudo systemctl restart ssh

### Mitigation

Edit `/etc/ssh/sshd_config` to disable insecure access:

    PermitRootLogin no
    PasswordAuthentication no
    AllowUsers studentuser

Then restart SSH:

    sudo systemctl restart ssh

---

## Task 3: Firewall and Network Security

**Objective**  
Illustrate the importance of proper firewall configuration to restrict unnecessary network exposure.

### Setup

Install a web server and allow HTTP traffic:

    sudo apt install apache2 -y
    sudo ufw allow 80

### Port Scanning

Use Nmap to identify open ports:

    nmap -sS -Pn <target-ip>

### Mitigation

Deny unnecessary access and enable the firewall:

    sudo ufw deny 80
    sudo ufw enable
    sudo ufw status

---

## Task 4: SUID and Privilege Escalation

**Objective**  
Demonstrate how SUID binaries can be exploited to gain elevated privileges.

### Setup

Copy and set SUID on the bash binary:

    sudo cp /bin/bash /home/studentuser/suidbash
    sudo chmod u+s /home/studentuser/suidbash

### Exploitation

Gain a root shell:

    /home/studentuser/suidbash -p
    whoami  # root

### Mitigation

Remove the SUID permission:

    sudo chmod u-s /home/studentuser/suidbash

---

## Task 5: Automated Security Auditing

**Objective**  
Create a script to monitor login attempts and active services.

### Setup

Create the following script as `security_check.sh`:

    #!/bin/bash
    echo "Checking login attempts:"
    lastb | head

    echo "Checking running services:"
    ps aux | grep -v grep | grep -E 'apache|ssh|nginx'

Make it executable:

    chmod +x security_check.sh

### Mitigation

Schedule the script to run hourly using `cron`:

    (crontab -l 2>/dev/null; echo "0 * * * * /home/studentuser/security_check.sh") | crontab -

---

## Task 6: Log Analysis and Intrusion Detection

**Objective**  
Review system logs to identify suspicious activity and configure automated blocking.

### Setup

Review system logs:

    sudo journalctl -xe
    sudo tail -f /var/log/auth.log

### Mitigation

Install and configure Fail2Ban:

    sudo apt install fail2ban -y
    sudo systemctl enable fail2ban
    sudo systemctl start fail2ban

---

## License

This content is provided under the MIT License.

## Author

Your Name
