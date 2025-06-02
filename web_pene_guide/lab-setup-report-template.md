#  **Lab Report: Deploy Virtual Machines in an Isolated Network**



###  **Project Title**

**Lab Setup: Deploy Virtual Machines**



###  **Project Supervisor**

**Norbert Ephraim**



###  **Prepared By**

*Your Name*
*Date of Submission*



## 1.  Project Overview

Add the project overview

## 2.  Project Goals

Add the project goals

## 3.  Lab Design & Considerations

### 3.1. Hypervisor Selection

* **Chosen Hypervisor:** 
* **Reason:** 

### 3.2. Networking Considerations

* **Mode Used:** 
* **Why:** 

| Hypervisor | Selected Mode | Isolation Level | Notes                                                |
| ---------- | ------------- | --------------- | ---------------------------------------------------- |
| VirtualBox |   | High            |  |

### 3.3. Operating System Images

| VM  | Operating System | ISO Version Used |
| --- | ---------------- | ---------------- |
| VM1 | Windows 10 Pro   | 22H2 (64-bit)    |
| VM2 | Ubuntu Linux     | 22.04 LTS        |



## 4.  Lab Implementation Steps

### 4.1. Install Hypervisor

* Downloaded and installed **VirtualBox** on Windows 11 host machine.

### 4.2. Download OS ISOs

* **Windows 10 ISO** downloaded from Microsoft official site.
* **Ubuntu 22.04 ISO** downloaded from ubuntu.com.

### 4.3. Create and Configure Virtual Machines

* **VM1 (Windows 10)**

  * 2 CPUs, 4GB RAM, 64GB disk
  * Network adapter set to "NAT Network"
* **VM2 (Ubuntu 22.04)**

  * 2 CPUs, 3GB RAM, 40GB disk
  * Network adapter set to same "NAT Network"

### 4.4. Network Configuration

* Created a custom NAT Network in VirtualBox
* Ensured both VMs were attached to the same NAT Network
* Disabled host access and external internet access to enforce isolation



## 5.  Validation & Testing

### 5.1. IP Address Verification

* Checked IPs using:

  * `ipconfig` (Windows VM IP)
  * `ip a` or `ifconfig` (Linux VM)

### 5.2. Connectivity Test

* **Ping Test:** From Windows VM to Linux VM

  * Command: `ping <Linux-VM-IP>`
  *  Successful response with <1ms latency



## 6.  Postmortem & Lessons Learned

###  What Went Well:

* VirtualBox’s NAT Network worked effectively for isolation.
* OS installation was smooth.
* Internal ping test confirmed correct VM communication setup.

###  Challenges:

* Initial misconfiguration by selecting "NAT" instead of "NAT Network".
* Needed to manually configure the NAT Network settings to enable DHCP.

###  Key Takeaways:

* Networking mode selection is critical to VM isolation.
* Hands-on VM setup builds foundational skills for penetration testing labs and malware analysis environments.
* Understanding hypervisor networking is essential for real-world cybersecurity lab design.



## 7.  Supporting Evidence

> *Attach screenshots of the following:*

* VirtualBox settings showing the NAT Network
* Windows and Linux VM desktops
* Terminal output showing successful `ping` test
* IP address configurations from both VMs



## 8.  References

* [VirtualBox Documentation](https://www.virtualbox.org/manual/)
* [Microsoft Windows ISO Download](https://www.microsoft.com/software-download/)
* [Ubuntu Linux ISO Download](https://ubuntu.com/download)
* [VM Networking Modes – Comparisons](https://www.geeksforgeeks.org/networking-modes-in-virtualbox/)

