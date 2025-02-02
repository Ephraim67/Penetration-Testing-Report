### **Detailed Guide: Manual and Automated Resource Discovery on a Secured Website**  

After intercepting traffic using Burp Suite or WebScarab, the next step is to manually and automatically discover hidden resources within the application. This guide will cover both manual and automated techniques.  

---

## **Step 1: Manually Request Valid and Invalid Resources**  

Start by sending **manual HTTP requests** to test how the server handles both valid and invalid resources.  

### **Manual Testing Using Burp Suite or Curl**  

1. **Identify Known Resources**  
   - Use Burp Suite’s **HTTP History** or **Site Map** to find valid URLs.  
   - Select key endpoints (e.g., `/dashboard`, `/api/user`).  

2. **Modify the Requests for Invalid Resources**  
   - Change valid URLs slightly (`/dashboard123`, `/api/admin`).  
   - Test common hidden paths (`/backup`, `/old`, `/dev`).  
   - Check how the server responds:
     - **404 Not Found** → Standard missing resource.  
     - **403 Forbidden** → Restricted but exists.  
     - **500 Internal Server Error** → Possible misconfigured backend.  
     - **302 Redirect** → May expose an admin panel or login page.  

### **Example Using Curl (Command Line Method)**  

```bash
curl -I -X GET "https://target.com/valid_page"
curl -I -X GET "https://target.com/invalid_page"
curl -I -X GET "https://target.com/admin"
```

Check if different HTTP response codes reveal hidden content.  

---

## **Step 2: Use the Site Map for Automated Hidden Content Discovery**  

Once you have mapped the site using Burp Suite’s **Spider**, use it to guide further automated exploration.  

1. **Go to Target > Site Map** in Burp Suite.  
2. Identify URLs that were discovered but not directly accessible.  
3. Use **right-click > Send to Intruder** to test additional variations.  
4. Take note of URLs that returned **non-404 responses**.  

---

## **Step 3: Automate Requests for Common Filenames and Directories**  

Use **Burp Intruder**, **ffuf**, or **Gobuster** to brute-force hidden directories and files.  

### **Using Burp Intruder**  

1. **Right-click on a request in Burp Suite** and select **Send to Intruder**.  
2. **Set the Target URL** (e.g., `https://target.com/`).  
3. **Configure Payload Positions**:
   - Replace a directory name with `§` (e.g., `https://target.com/§admin§/`).
4. **Use a Wordlist**:
   - Load a common directory list (`/usr/share/wordlists/dirb/common.txt`).
5. **Start the Attack** and analyze responses:
   - **Different response lengths** may indicate valid resources.  

### **Using Gobuster (CLI Method)**  

```bash
gobuster dir -u https://target.com -w /usr/share/wordlists/dirb/common.txt
```

### **Using FFUF (Faster Alternative to Gobuster)**  

```bash
ffuf -u https://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt
```

---

## **Step 4: Capture and Review Responses**  

After running automated scans:  

1. **Analyze responses for valid resources**.  
2. **Look for directories returning 200 OK or 403 Forbidden**.  
3. **Manually access and explore new URLs discovered**.  
4. **Perform Recursive Scans**:
   - If a new directory is found (e.g., `/admin/`), repeat the scan within it.  

---

### **Final Thoughts**  

Following this approach will allow you to:  
- Identify hidden files (`.git`, `.env`, `backup.zip`).  
- Detect exposed APIs or admin panels.  
- Uncover test or old versions of the application.
