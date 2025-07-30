# Linux & Networking POC Lab


## Objective

This lab introduces essential Linux commands, networking tools, and automation techniques through ten hands-on tasks. By completing these exercises, students will gain practical experience in system administration.

---

## Task 1: Linux Essentials & File Permissions

**Steps:**

1. Create a user:

    ```bash
    sudo adduser studentuser
    ```

2. Create the directory structure:

    ```bash
    mkdir -p /home/studentuser/projectX/logs
    mkdir -p /home/studentuser/projectX/scripts
    ```

3. Create the file `welcome.txt`:

    ```bash
    echo "Welcome to Linux" > /home/studentuser/projectX/welcome.txt
    ```

4. Set secure permissions:

    ```bash
    chown studentuser:studentuser /home/studentuser/projectX/welcome.txt
    chmod 600 /home/studentuser/projectX/welcome.txt
    ```

5. Create `backup.sh` script in `scripts/`:

    ```bash
    cat << 'EOF' > /home/studentuser/projectX/scripts/backup.sh
    #!/bin/bash
    timestamp=$(date +"%Y%m%d_%H%M%S")
    cp /home/studentuser/projectX/welcome.txt /home/studentuser/projectX/logs/welcome_$timestamp.txt
    EOF

    chmod +x /home/studentuser/projectX/scripts/backup.sh
    ```

---

## Task 2: Networking Toolkit POC

1. Create `netinfo.sh`:

    ```bash
    cat << 'EOF' > netinfo.sh
    #!/bin/bash
    echo "IP Information:"
    ip a

    echo -e "\nDefault Gateway:"
    ip route | grep default

    echo -e "\nOpen Ports:"
    ss -tuln

    echo -e "\nPing google.com:"
    ping -c 4 google.com

    echo -e "\nDNS Lookup for openai.com:"
    nslookup openai.com
    EOF

    chmod +x netinfo.sh
    ./netinfo.sh > network_report.txt
    ```

---

## Task 3: Mini Server Monitor Script

1. Create `monitor.sh`:

    ```bash
    cat << 'EOF' > monitor.sh
    #!/bin/bash
    logfile="/var/log/monitor.log"
    echo "------ $(date) ------" >> $logfile

    if ! pgrep nginx > /dev/null; then
        echo "nginx not running. Starting..." >> $logfile
        sudo systemctl start nginx
    else
        echo "nginx is running." >> $logfile
    fi

    echo "Memory Usage:" >> $logfile
    free -h >> $logfile

    echo "CPU Load:" >> $logfile
    uptime >> $logfile

    echo "Disk Usage:" >> $logfile
    df -h >> $logfile
    EOF

    chmod +x monitor.sh
    ```

2. Schedule it every 5 minutes:

    ```bash
    crontab -e
    # Add the line:
    */5 * * * * /path/to/monitor.sh
    ```

---

## Task 4: File Watcher Script

1. Create `watch_dir.sh`:

    ```bash
    cat << 'EOF' > watch_dir.sh
    #!/bin/bash
    inotifywait -m /home/studentuser/projectX/logs -e create -e moved_to |
    while read path action file; do
        if [[ "$file" == *.txt ]]; then
            echo "$(date): New file detected - $file" >> /home/studentuser/projectX/logs/log_monitor.txt
        fi
    done
    EOF

    chmod +x watch_dir.sh
    ```

---

## Task 5: SSH Login Audit

1. Create the script:

    ```bash
    cat << 'EOF' > ssh_audit.sh
    #!/bin/bash
    echo "Last 5 Successful Logins:" > ssh_audit.txt
    last -a | head -n 5 >> ssh_audit.txt

    echo -e "\nLast 5 Failed Logins:" >> ssh_audit.txt
    journalctl _COMM=sshd | grep "Failed password" | tail -n 5 >> ssh_audit.txt
    EOF

    chmod +x ssh_audit.sh
    ./ssh_audit.sh
    ```

---

## Task 6: Crontab Practice

1. Open crontab:

    ```bash
    crontab -e
    ```

2. Add the following lines:

    ```cron
    # Good morning message at 8 AM daily
    0 8 * * * echo "Good morning!"

    # Weekly backup every Sunday
    0 2 * * 0 cp -r /home/studentuser/projectX /home/studentuser/projectX/backup/

    # Delete old .log files every Friday at midnight
    0 0 * * 5 find /home/studentuser/projectX/ -name "*.log" -mtime +7 -delete
    ```

---

## Task 7: Port Scanner Script

1. Create `port_scan.sh`:

    ```bash
    cat << 'EOF' > port_scan.sh
    #!/bin/bash
    read -p "Enter IP address: " ip
    for port in {20..25}; do
        timeout 1 bash -c "echo > /dev/tcp/$ip/$port" 2>/dev/null && echo "Port $port is open"
    done
    EOF

    chmod +x port_scan.sh
    ```

---

## Task 8: Website Availability Checker

1. Prepare `sites.txt` with one URL per line.

2. Create `check_sites.sh`:

    ```bash
    cat << 'EOF' > check_sites.sh
    #!/bin/bash
    while read url; do
        if curl -Is "$url" | grep "200 OK" > /dev/null; then
            echo "$url is UP" >> site_status.log
        else
            echo "$url is DOWN or UNREACHABLE" >> site_status.log
        fi
    done < sites.txt
    EOF

    chmod +x check_sites.sh
    ./check_sites.sh
    ```

---

## Task 9: Environment and Disk Report

1. Create `env_report.sh`:

    ```bash
    cat << 'EOF' > env_report.sh
    #!/bin/bash
    echo "User: $(whoami)"
    echo "Hostname: $(hostname)"
    echo "Uptime: $(uptime -p)"
    echo -e "\nMounted Filesystems:"
    df -h
    echo -e "\nEnvironment Variables:"
    echo "\$PATH=$PATH"
    echo "\$SHELL=$SHELL"
    EOF

    chmod +x env_report.sh
    ./env_report.sh > env_report.txt
    ```

---

## Task 10: Compress and Archive Automation

1. Create `archive_logs.sh`:

    ```bash
    cat << 'EOF' > archive_logs.sh
    #!/bin/bash
    archive_name="archive_$(date +%Y%m%d).tar.gz"
    find /home/studentuser/projectX/logs -name "*.log" -size +10M -print0 | tar -czvf $archive_name --null -T -
    mv $archive_name /home/studentuser/projectX/backup/
    EOF

    chmod +x archive_logs.sh
    ./archive_logs.sh
    ```

---


## Author

[Vishal RJ]
