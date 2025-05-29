## What is Password Reset Poisoning?

When an application **builds password reset links based on user-controlled headers** like `Host` or `X-Forwarded-Host`, an attacker can **inject their own domain**, causing the reset link to be sent with a **malicious host**, leading to **account takeover**.



##  Step-by-Step Guide

###  Step 1: Go to Password Reset Page

* Visit the target site (e.g. `https://example.com/forgot-password`)
* Enter the **email address of your own test account** and submit the password reset form.



###  Step 2: Intercept the Request in Burp Suite

* Open **Burp Suite Proxy**.
* Make sure your browser is using Burp as the proxy.
* Intercept the **password reset request** — usually a **POST** request like:

  ```http
  POST /reset.php HTTP/1.1
  Host: example.com
  Content-Type: application/json

  {
    "email": "victim@example.com"
  }
  ```



###  Step 3: Modify Headers to Inject Malicious Host

* In the intercepted request, **change or add these headers**:

  ```http
  Host: attacker.com
  X-Forwarded-Host: attacker.com
  ```

  So now your request looks like:

  ```http
  POST /reset.php HTTP/1.1
  Host: attacker.com
  X-Forwarded-Host: attacker.com
  Content-Type: application/json

  {
    "email": "victim@example.com"
  }
  ```

>  The key idea: You're trying to **trick the backend** into building a password reset link like:

```
https://attacker.com/reset-password.php?token=XYZ
```

Instead of the legitimate domain.



### Step 4: Forward the Request

* Forward the modified request from Burp Suite to the server.



###  Step 5: Check the Password Reset Email

* Open the inbox of `victim@example.com`.
* Look at the **reset link** in the email.
* If it points to **`https://attacker.com`** instead of `https://example.com`, then:

>  **The application is vulnerable!**

This means an attacker can:

* Initiate a reset for a victim's email.
* Receive the reset link to **their own server**.
* Use the token to reset the victim's password → **Account Takeover**



##  How Should Developers Prevent This?

* **Never trust user-supplied headers** (`Host`, `X-Forwarded-Host`, etc.).
* Hardcode the domain when generating links (e.g., `https://example.com/reset-password?...`)
* Use a secure Referrer Policy and strict origin checks.
* If behind a proxy, validate `X-Forwarded-*` headers correctly and securely.
