# FTP Service Enumeration & vsftpd 2.3.4 Backdoor Exploitation (Kali + Metasploitable)

What this project is

In this lab, you built a controlled red-team style workflow to:

verify network connectivity between attacker and victim VMs,

enumerate services running on the victim (Metasploitable),

identify a vulnerable FTP service (vsftpd 2.3.4),

exploit the service using Metasploit,

validate impact by confirming root-level access,

document evidence with screenshots.


Lab Environment

Attacker VM: Kali Linux (VirtualBox)
Victim VM: Metasploitable (VirtualBox)
Network: Same subnet (your screenshots show 192.168.56.0/24)
Tools used:

Linux networking tools (interface + routing checks)

Nmap (service/version enumeration)

Metasploit Framework (exploit execution + session handling)
