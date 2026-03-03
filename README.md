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

Linux networking tools (interface + routing checks)

Nmap (service/version enumeration)

Metasploit Framework (exploit execution + session handling)

---

## Step-by-Step Attack Narrative 
Phase 1 — Lab Networking Validation (Victim)

Objective: Confirm Metasploitable is on the same lab network and has a usable IP.

### Step 1.1 — Check victim network interfaces

I checked the victim’s network configuration and saw it did not yet have an IPv4 address (only loopback / link-local behavior).

Evidence: 01_metasploitable_ifconfig_no_ipv4.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/1dc78243-133b-4b75-b4ea-a86a0b772930" />


### Step 1.2 — Acquire/confirm DHCP lease

I triggered/observed DHCP activity and confirmed Metasploitable received an address: 192.168.56.101.

This is the key prerequisite before scanning.

Evidence: 02_metasploitable_dhcp_ip_assigned.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/3f4a2931-a8da-40e3-9554-6b9af57d3aeb" />


### Step 1.3 — Verify routing on the victim

I checked the routing table to confirm the interface/network route existed (basic connectivity sanity check).

Evidence: 03_metasploitable_route_table_check.jpg

<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/6aea6263-9146-462f-b123-991869b31e79" />


## Phase 2 — Lab Networking Validation (Attacker)

Objective: I Confirmed Kali is on the same subnet and ready to reach the target.

Step 2.1 — Identify attacker IP

I confirmed Kali’s active interface and IP: 192.168.56.102 (same /24 network as the victim).

This validates attacker ↔ victim reachability in the lab.

Evidence: 04_kali_ifconfig_attacker_ip.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/54bf2ba2-a539-4955-b432-0582fa36d27a" />


## Phase 3 — Reconnaissance & Service Discovery

Objective: I Identified open ports and exposed services on the target.

Step 3.1 — Initial discovery scan

And I ran a scan to determine:

- What ports are open

- What services are running

- What versions are exposed (useful for vulnerability mapping)

- I identified FTP running vsftpd 2.3.4 and additional remote-access services.

Evidence: 05_kali_nmap_initial_service_discovery.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/140f8e15-30f5-4f7b-9b86-fda56973d152" />

## Phase 4 — Enumeration (Deep Target Profiling)

Objective: Expand findings into a clear “attack surface map” (what’s exposed, what’s risky, what’s likely exploitable).

#### Step 4.1 — Enumerate more services

I documented additional services typically present on Metasploitable 2 (web, RPC, file sharing, databases, etc.).

The point here is prioritization: older/insecure services + remote access = high risk.

Evidence: 06_kali_nmap_full_enumeration_services.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/a151e71a-c844-4ab7-81a5-377b9bcba3fa" />


#### Step 4.2 — Collect OS/service metadata

I captured OS guesses, script results, and network distance (useful for validation and reporting).

Evidence: 07_kali_nmap_os_scripts_traceroute.jpg
<img width="1536" height="1152" alt="image" src="https://github.com/user-attachments/assets/392b7696-d4ba-4a76-9207-9bfaffb614a4" />
