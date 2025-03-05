Practical examples for testing Web Authentication, Session Management, and Access Control vulnerabilities:

---

### **1. Web Authentication Testing**
**Objective:** Identify weaknesses in the authentication mechanism, such as weak password policies, lack of multi-factor authentication, or session token leaks.

#### **Test Cases**
- **Brute Force Attacks:**  
  - Use tools like Burp Suite Intruder, Hydra, or wfuzz to brute-force login credentials.
  - Example: Try a dictionary attack using common passwords on the login form.
  
- **Credential Stuffing:**  
  - Use leaked credential lists from breaches to test if users reuse passwords.
  
- **Weak Password Policies:**  
  - Check if the application allows weak passwords (e.g., "password123", "admin123").
  
- **Session Token Leakage in URL:**  
  - Log in and check if the session token appears in the URL (e.g., `https://example.com/profile?sessionid=abc123`).
  
- **Insecure Password Reset Mechanism:**  
  - Test if the password reset feature is exploitable (e.g., predictable reset tokens, lack of rate limiting).
  - Example: Try changing the email in the reset URL (`reset?token=123&email=hacker@example.com`).

---

### **2. Session Management Testing**
**Objective:** Identify vulnerabilities that allow session hijacking, session fixation, or improper session termination.

#### **Test Cases**
- **Session Hijacking via Cookie Theft:**
  - Use tools like Burp Suite or browser extensions to inspect and steal session cookies.
  - Example: Copy the session cookie from an authenticated session and use it in another browser to see if it grants access.

- **Session Fixation:**
  - Example: Set a predefined session ID before authentication (e.g., `sessionid=1234`) and see if it remains valid after login.
  
- **Improper Session Expiration:**
  - Log in and leave the session idle for some time. Check if the session expires or remains active.
  
- **Cross-Site Scripting (XSS) for Session Stealing:**
  - Inject JavaScript (`document.cookie`) in input fields to capture session cookies.

- **Cookie Security Flags:**
  - Check if `HttpOnly`, `Secure`, `SameSite`, and `Domain` attributes are set properly.
  - Example: If `Secure` is missing, test if session cookies can be stolen over HTTP.

---

### **3. Access Control Testing**
**Objective:** Identify privilege escalation, unauthorized access, and broken access control vulnerabilities.

#### **Test Cases**
- **Horizontal Privilege Escalation:**
  - Log in as a low-privileged user and try accessing other users’ data.
  - Example: Change the `user_id=123` in a request to `user_id=456` and check if data is accessible.

- **Vertical Privilege Escalation:**
  - Log in as a regular user and try accessing admin features.
  - Example: Modify the request from `GET /user/dashboard` to `GET /admin/panel`.

- **IDOR (Insecure Direct Object References):**
  - Example: If downloading an invoice (`/download/invoice?id=1001`), try changing the ID to `1002`.

- **Forced Browsing:**
  - Manually or with tools like OWASP ZAP, check for hidden pages.
  - Example: Try accessing `/admin`, `/config.php`, `/backup.zip`.

- **API Access Control:**
  - Use Burp Suite to modify API requests.
  - Example: Test API endpoints without authentication or using a low-privileged API key.
