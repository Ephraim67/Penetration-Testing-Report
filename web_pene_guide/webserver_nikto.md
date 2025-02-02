### **Using Nikto for Web Server Scanning**  

Nikto is a widely used open-source tool for scanning web servers for vulnerabilities, misconfigurations, and known security issues. Below is a detailed guide on how to effectively use Nikto in penetration testing.  

---

## **Step 1: Basic Nikto Scan**  

To run a simple scan against a target web server:  

```bash
nikto -h https://target.com
```

This command:  
- Scans for common vulnerabilities.  
- Identifies outdated software.  
- Detects misconfigurations and security issues.  

---

## **Step 2: Running Nikto with Useful Options**  

### **1. Specifying an Alternative Root Directory**  

If the web server hosts interesting files in a non-standard directory (e.g., `/cgi/cgi-bin` instead of `/cgi-bin`), use the `-root` option:  

```bash
nikto -h https://target.com -root /cgi/
```

For scanning specific CGI directories:  

```bash
nikto -h https://target.com -Cgidirs /cgi-bin,/custom-cgi
```

---

### **2. Handling Custom 404 Pages**  

Some websites use custom "file not found" pages that do not return an actual `404 Not Found` HTTP status. This can cause Nikto to falsely interpret non-existing pages as valid. To avoid this, specify a unique string from the custom 404 page using `-404`:  

```bash
nikto -h https://target.com -404 "Page Not Found"
```

---

### **3. Specifying Ports and Protocols**  

Nikto scans port 80 by default. To specify a different port (e.g., 8080), use:  

```bash
nikto -h https://target.com -p 8080
```

For multiple ports:  

```bash
nikto -h https://target.com -p 80,443,8080
```

To force SSL/TLS scanning (if not automatically detected):  

```bash
nikto -h https://target.com -ssl
```

---

### **4. Controlling Scan Speed and Timeout**  

To slow down the scan and avoid detection by security systems:  

```bash
nikto -h https://target.com -T 3
```

where `-T` (Tuning) has levels from `0` (fastest) to `6` (slowest).  

Set a timeout for responses:  

```bash
nikto -h https://target.com -timeout 10
```

---

### **5. Outputting Results to a File**  

To save scan results in different formats:  

```bash
nikto -h https://target.com -o results.txt
nikto -h https://target.com -o results.html -Format htm
nikto -h https://target.com -o results.json -Format json
```

---

### **6. Virtual Host and IP Scanning Considerations**  

Many web applications are **virtually hosted**, meaning multiple domains share the same IP address. When using Nikto, always specify the correct hostname to avoid missing vulnerabilities.  

```bash
nikto -h target.com -H "Host: target.com"
```

To scan using an IP address while treating the domain correctly:  

```bash
nikto -h 192.168.1.10 -host target.com
```

---

## **Step 3: Manually Verifying Nikto Findings**  

Nikto is prone to false positives, so:  
1. **Review the results manually** before acting on them.  
2. **Cross-check with other tools** like Nmap, Dirb, or Burp Suite.  

Example Nmap scan for web vulnerabilities:  

```bash
nmap --script=http-vuln* -p 80,443 target.com
```

Nikto is a powerful tool for identifying web vulnerabilities but should be combined with manual testing and other scanning tools to confirm findings. Would you like guidance on integrating Nikto with other testing methodologies?
