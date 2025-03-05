### **Testing the Secure Cookie Attribute Using Burp Suite**  

The **Secure** attribute ensures that cookies are only transmitted over encrypted HTTPS connections, protecting session IDs from being intercepted in **Man-in-the-Middle (MitM) attacks**. Below is a step-by-step guide to testing this attribute using **Burp Suite**.

---

## **1. Capturing Cookies in Burp Suite**
### **Steps:**
1. **Start Burp Suite** and configure your browser to use Burp as a proxy.
2. **Navigate to the target website** and log in (or perform an action that sets a session cookie).
3. Go to **Proxy > HTTP history** in Burp Suite.
4. Look for a **Set-Cookie** header in the **Response** tab.

---

## **2. Checking for the Secure Attribute**
### **Steps:**
1. In **Burp Suite**, find the `Set-Cookie` response header.
2. Look for the `Secure` attribute in the session cookie.

### **Example: Secure vs. Insecure Cookies**
#### **Secure Cookie (Correct Implementation)**
```
Set-Cookie: SESSIONID=abc123; Secure; HttpOnly; SameSite=Strict
```
- The `Secure` flag ensures the cookie is only sent over HTTPS.

#### **Insecure Cookie (Vulnerable Implementation)**
```
Set-Cookie: SESSIONID=abc123; HttpOnly; SameSite=Strict
```
- Missing the `Secure` attribute means the session cookie **can be transmitted over HTTP**, making it vulnerable to interception.

---

## **3. Forcing the Application to Leak Cookies Over HTTP**
### **Steps:**
1. Open Burp Suite’s **Repeater** tool.
2. Send a request over **HTTP (not HTTPS)** to the same domain.
3. Check the response to see if the cookie is included in the request.

### **Vulnerable Behavior:**
- If the session cookie is sent over HTTP, it is **exposed to network sniffing**.
- An attacker can capture and reuse the session ID, leading to **session hijacking**.

---

## **4. Mitigation: Enforcing Secure Cookies**
### **Fix Recommendations:**
- **Enable the Secure Attribute** on all session cookies.
- **Use HSTS (HTTP Strict Transport Security)** to enforce HTTPS.
- **Redirect all HTTP requests to HTTPS** at the server level.

### **Correct Cookie Configuration**
```
Set-Cookie: SESSIONID=abc123; Secure; HttpOnly; SameSite=Strict
```
- Ensures the session ID **cannot be transmitted over HTTP**.

---

## **5. Conclusion**
| **Test** | **Expected Behavior** | **Vulnerable Behavior** |
|----------|----------------------|------------------------|
| Secure Attribute Present | Cookie only sent over HTTPS | Cookie sent over HTTP |
| HTTP Request Test | Cookie not transmitted | Cookie leaked over HTTP |
| Burp Suite Repeater | No session cookie over HTTP | Session ID visible over HTTP |
