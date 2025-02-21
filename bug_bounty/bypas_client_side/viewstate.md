### **Detailed Steps for Attacking ViewState in an ASP.NET Application**

When testing an **ASP.NET application**, the **ViewState** mechanism can be a potential attack vector. ViewState is a method used by ASP.NET to maintain the state of web controls across postbacks. If it is not **properly protected**, an attacker may be able to manipulate it to execute **privilege escalation, tampering, or even remote code execution (RCE)**.

Let’s go step by step.

---

## **Step 1: Check if MAC Protection is Enabled**
**(Message Authentication Code (MAC) protection ensures ViewState integrity)**

### **How to check for MAC protection:**
1. Open **Burp Suite** and intercept a request where ViewState is used.
2. Look for the **`__VIEWSTATE`** parameter in the request body or URL:
   ```
   __VIEWSTATE=/wEPDwULLTExMzIxMjA0OTYPZBYCZg9kFgICAw9kFg...
   ```
3. Decode the ViewState using Burp's **ViewState parser**:
   - Go to **Decoder** tab → Select **Decode as ViewState**.
4. If a **20-byte hash (HMAC) is present at the end**, MAC protection is enabled.
   - Example **MAC-protected ViewState**:
     ```
     /wEPDwUJNDA5MTI3ODgwZGQ2WUlS
     ```
   - Example **Unprotected ViewState** (no hash):
     ```
     /wEPDwUKLTc4OTM2MjM5N2Rk
     ```
5. If MAC protection **is not enabled**, the ViewState is vulnerable to **tampering and replay attacks**.

**Bug Example:**
A researcher found an **ASP.NET admin portal** without MAC protection. By modifying the ViewState, they were able to **escalate privileges**.

---

## **Step 2: Decode ViewState to Look for Sensitive Data**
Even if ViewState is **protected**, it may still **leak sensitive information**.

### **How to decode ViewState in Burp Suite:**
1. Copy the **base64-encoded ViewState** from the intercepted request.
2. Go to **Burp Suite Decoder** → Select **Decode as ViewState**.
3. Review the decoded output for:
   - **User IDs**
   - **Session tokens**
   - **Roles and permissions**
   - **Pricing or business logic data**

**Bug Example:**
A researcher found that ViewState contained **user email and session details**. This could allow **session hijacking** if combined with other attacks.

---

## **Step 3: Modify ViewState Parameters & Observe Errors**
If ViewState contains **parameters** (e.g., user roles, permissions, prices), try **modifying** them.

### **How to modify ViewState safely:**
1. Decode ViewState using Burp Suite.
2. Identify an interesting parameter, such as:
   ```
   "UserRole": "Customer"
   ```
3. Change it to:
   ```
   "UserRole": "Admin"
   ```
4. Encode the modified ViewState and send the request.
5. If **no error appears**, check if the role has changed.

### **If an error occurs:**
- **"ViewState validation failed"** → MAC protection is enforced.
- **No error, but no effect** → The parameter might not be used.
- **Application crash** → Possible security misconfiguration.

**Bug Example:**
A researcher modified ViewState in an **insurance portal** and changed their **plan from "Basic" to "Premium"** without payment.

---

## **Step 4: Identify Custom Data & Test for Vulnerabilities**
Some applications store **custom data** inside ViewState, such as:
- **Product prices**
- **Discounts and promotions**
- **User access levels**

### **How to exploit this:**
1. Find a **custom parameter** inside ViewState:
   ```
   "DiscountRate": "0%"
   ```
2. Modify it:
   ```
   "DiscountRate": "100%"
   ```
3. Encode the modified ViewState and submit it.

#### **Common vulnerabilities to check:**
- **Privilege escalation** → Changing roles (`User` → `Admin`).
- **Price manipulation** → Modifying product price before checkout.
- **Business logic bypass** → Submitting unauthorized discounts.

**Bug Example:**
A security researcher exploited ViewState in an **online learning platform**, changing their subscription from **"Trial" to "Lifetime Membership"**.

---

## **Step 5: Test ViewState on Multiple Pages**
MAC protection **may be disabled on some pages**. Attackers should **test ViewState across different pages**.

### **How to automate this with Burp Scanner:**
1. Enable **passive scanning** in Burp Suite.
2. Browse the target application normally.
3. Burp will **automatically detect** any ViewState without MAC protection.

### **Why test multiple pages?**
- Some **pages have MAC disabled** (e.g., login, password reset).
- If MAC protection is **only enforced on some pages**, attackers can **bypass it by sending ViewState from an unprotected page**.

**Bug Example:**
A researcher found that **ViewState MAC was enforced on the dashboard** but **disabled on the login page**. By modifying ViewState, they **logged in as another user**.

---

## **Key Takeaways for Deriv Bug Bounty Testing**
- **Look for `__VIEWSTATE`** in requests & responses.
- **Use Burp Suite** to decode ViewState.
- **Modify parameters** to check if ViewState is vulnerable.
- **Test multiple pages**, as some might lack MAC protection.
- **Submit crafted payloads** to test for privilege escalation, price tampering, or business logic flaws.
