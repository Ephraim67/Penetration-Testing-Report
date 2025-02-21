### **Real-World Bug Bounty Example: Privilege Escalation via Hidden Form Field**

#### **Target: A SaaS-based Admin Dashboard**
A security researcher on **HackerOne** was testing an admin panel that allowed users to manage their accounts and perform administrative tasks.

#### **Step 1: Identifying Hidden Form Fields**
While inspecting the **"Edit Profile"** feature, the researcher found this hidden field in the HTML form:

```html
<input type="hidden" name="role" value="user">
```
This suggested that the user’s role was being stored in the form and sent to the server.

#### **Step 2: Understanding the Role in Application Logic**
By analyzing the request in **Burp Suite**, the researcher saw the following request when updating their profile:

```
POST /update_profile HTTP/1.1
Host: target.com
Content-Type: application/x-www-form-urlencoded

username=testuser&email=test@target.com&role=user
```
This indicated that the application **was trusting** the client-provided role value.

#### **Step 3: Modifying the Value to "admin"**
The researcher changed:

```
role=user
```
to:

```
role=admin
```
and sent the request.

#### **Outcome: Gained Admin Access**
Upon checking the dashboard, the researcher found they now had **full admin privileges**, including:
- Viewing all users' details
- Resetting passwords
- Accessing restricted admin-only pages

This was a **Critical Privilege Escalation Vulnerability** because **authorization** was being controlled client-side instead of validated server-side.

#### **Impact:**
- An attacker could grant themselves **administrator privileges**.
- They could delete users, modify settings, or even perform **account takeovers**.

#### **Remediation Suggested:**
- **Server-side validation**: The server should **ignore client-sent "role" values** and determine privileges based on the session.
- **Role-based access control (RBAC)**: Ensure authorization checks are enforced for each action.
- **Use HTTP-only, secure cookies** for authentication instead of hidden fields.

#### **Bug Bounty Reward:**
The company fixed the issue and awarded the researcher **$5,000** under their **HackerOne bug bounty program**.

---

### **Key Takeaways**
1. Look for **hidden fields** that store roles, permissions, or user data.
2. Check if **changing values grants unauthorized access**.
3. Always **modify cookies, URL parameters, and form fields** to test server validation.
4. Report findings with **clear PoCs (Proof of Concepts)**.
