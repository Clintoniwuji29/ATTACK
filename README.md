# FTP Service Enumeration & vsftpd 2.3.4 Backdoor Exploitation (Kali + Metasploitable)

What this project is:

In an isolated VirtualBox lab, I validated network connectivity, identified exposed services on Metasploitable 2, confirmed a vulnerable FTP service/version, and used a framework module to demonstrate controlled remote access—then documented what a defender should detect and fix.
In this lab, I built a controlled red-team style workflow to:

- verify network connectivity between attacker and victim VMs,

- enumerate services running on the victim (Metasploitable),

- identify a vulnerable FTP service (vsftpd 2.3.4),

- exploit the service using Metasploit,

- validate impact by confirming root-level access,

- document evidence with screenshots.


### Scope / Rules of Engagement

Target: Metasploitable 2 (intentionally vulnerable VM)

Attacker: Kali Linux VM

Environment: Local VirtualBox host-only / internal lab network

Constraints: Lab-only, no internet-facing targets, no real systems

Goal: Demonstrate the red-team workflow (not “damage”): find → verify → gain access → validate impact → recommend remediation

### Lab Environment

Attacker VM: Kali Linux (VirtualBox)
Victim VM: Metasploitable (VirtualBox)
Network: Same subnet (192.168.56.0/24)
Tools used:

- Linux networking tools (interface + routing checks)

- Nmap (service/version enumeration)

- Metasploit Framework (exploit execution + session handling)

---

# Step-by-Step Attack Narrative 
## Phase 1 — Lab Networking Validation (Victim)

Objective: Confirm Metasploitable is on the same lab network and has a usable IP.

### Step 1.1 — Check victim network interfaces

I checked the victim’s network configuration and saw it did not yet have an IPv4 address (only loopback / link-local behavior).

> Evidence: 01_metasploitable_ifconfig_no_ipv4.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/1dc78243-133b-4b75-b4ea-a86a0b772930" />


### Step 1.2 — Acquire/confirm DHCP lease

I triggered/observed DHCP activity and confirmed Metasploitable received an address: 192.168.56.101.

This is the key prerequisite before scanning.

> Evidence: 02_metasploitable_dhcp_ip_assigned.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/3f4a2931-a8da-40e3-9554-6b9af57d3aeb" />


### Step 1.3 — Verify routing on the victim

I checked the routing table to confirm the interface/network route existed (basic connectivity sanity check).

> Evidence: 03_metasploitable_route_table_check.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/6aea6263-9146-462f-b123-991869b31e79" />

---

## Phase 2 — Lab Networking Validation (Attacker)

Objective: I Confirmed Kali is on the same subnet and ready to reach the target.

Step 2.1 — Identify attacker IP

I confirmed Kali’s active interface and IP: 192.168.56.102 (same /24 network as the victim).

This validates attacker ↔ victim reachability in the lab.

> Evidence: 04_kali_ifconfig_attacker_ip.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/54bf2ba2-a539-4955-b432-0582fa36d27a" />


---

## Phase 3 — Reconnaissance & Service Discovery

Objective: I Identified open ports and exposed services on the target.

Step 3.1 — Initial discovery scan

And I ran a scan to determine:

- What ports are open

- What services are running

- What versions are exposed (useful for vulnerability mapping)

- I identified FTP running vsftpd 2.3.4 and additional remote-access services.

> Evidence: 05_kali_nmap_initial_service_discovery.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/140f8e15-30f5-4f7b-9b86-fda56973d152" />


---

## Phase 4 — Enumeration (Deep Target Profiling)

Objective: Expand findings into a clear “attack surface map” (what’s exposed, what’s risky, what’s likely exploitable).

#### Step 4.1 — Enumerate more services

I documented additional services typically present on Metasploitable 2 (web, RPC, file sharing, databases, etc.).

The point here is prioritization: older/insecure services + remote access = high risk.

> Evidence: 06_kali_nmap_full_enumeration_services.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/a151e71a-c844-4ab7-81a5-377b9bcba3fa" />


#### Step 4.2 — Collect OS/service metadata

I captured OS guesses, script results, and network distance (useful for validation and reporting).

> Evidence: 07_kali_nmap_os_scripts_traceroute.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/392b7696-d4ba-4a76-9207-9bfaffb614a4" />


---

## Phase 5 — Vulnerability Validation (Targeted Service)

Objective: I Validated that the FTP service identified is truly relevant and tied to an exploitable condition (in a lab).

### Step 5.1 — Attempt to “locate” vsftpd locally on the victim

I attempted commands that show common mistakes:

This step demonstrates your troubleshooting process and how I corrected course by validating from Kali instead.



> Evidence: 08_metasploitable_vsftpd_check_mistakes.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/2f292115-7c2a-49ea-8230-c80a50e134af" />


---

## Phase 6 — Controlled Exploitation & Access Validation (Lab Only)

Objective: Demonstrate controlled access using a framework module aligned to the discovered vulnerable FTP service/version.

### Step 6.1 — Launch exploitation framework

I started Metasploit to search for an appropriate module that matches the discovered FTP service/version.



> Evidence: 09_kali_msfconsole_launch_banner.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/44d1faf1-b687-477e-8570-2c4b12bc5f4a" />

### Step 6.2 — Execute controlled exploit & validate impact

I selected a module aligned to the vulnerable FTP service/version and configured it to target 192.168.56.101.

Outcome: you established a remote session and validated privilege context (whoami shows root).

This proves a critical business risk: unauthenticated/remote compromise due to exposed vulnerable service.



> Evidence: 10_kali_metasploit_exploit_validation_root.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/2540279d-7486-4020-b5bf-63eb3afb4f64" />


## Phase 7 -  launched Metasploit and searched for modules matching:

> search vsftpd

#### I identified:

> exploit/unix/ftp/vsftpd_234_backdoor
#### Why This Is Important

This demonstrates how:
- I correlated Nmap version detection (vsftpd 2.3.4)

- I mapped version → known exploit module

- I selected the correct exploit based on service fingerprinting

Afterwards i configured the module:

> set RHOSTS 192.168.56.101
> run

Metasploit output:

Backdoor service has been spawned
UID: uid=0(root)
Command shell session opened

And i then validated access:

> whoami

> root


#### What This Proves

That i achieved:

- Remote code execution

- Root-level privilege access

- Full system compromise

This is a complete critical severity breach scenario.

- This is not partial access.
- This is full administrative control.

> Evidence: 11
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/a4192f66-0dac-43ac-9672-a6f5af572638" />

---

## Phase 8 - What Happened (Critical Step)

After gaining root, I began post-exploitation enumeration:

> pwd

> ls

> cat /etc/passwd

I successfully accessed and read /etc/passwd.

#### Significance of this: I did not stop at exploitation.

- I validated system-level file access

- I demonstrated impact by enumerating local accounts

- I showed understanding of Linux privilege structure


#### I moved into:

- Local user enumeration

- Service account identification

- Potential lateral movement analysis

  
> Evidence: 12
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/d35c4f2e-3dd1-412d-baf4-12cd163f8e42" />

## Achievement Summary:

### What I accomplished in this lab is not “just running Metasploit.” I demonstrated the ability to:

- Correctly configure a segmented lab network

- Validate routing and IP assignments

- Perform active reconnaissance using Nmap

- Interpret service banners and versions

- Identify vulnerable service exposure

- Map discovered version → known exploit

- Configure and execute a targeted exploit

- Successfully obtain a root shell

- Verify privilege escalation status

- Perform post-compromise enumeration

- Access sensitive system files

- Understand account structure and privilege boundaries

- I executed the full red team lifecycle.

---
