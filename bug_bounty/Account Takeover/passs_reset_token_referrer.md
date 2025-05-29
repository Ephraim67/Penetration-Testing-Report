###  What is this vulnerability?

This occurs when a **password reset token** (usually embedded in a URL) gets sent to a **third-party website via the `Referer` header** — potentially leaking sensitive information.


##  Step-by-Step Guide

###  **1. Request Password Reset**

* Go to the login page of the target website.
* Click on "Forgot Password" or "Reset Password".
* Enter your **email address** and submit the form.
* The site should send you a **password reset email** with a **link that contains a token**, like:

  ```
  https://example.com/reset-password?token=abc123
  ```



###  **2. Click the Password Reset Link**

* Open your **email inbox**.
* Click on the **reset password link** you received.
* This takes you to the **password reset page** — but **do not change your password** yet.



###  **3. Open a 3rd Party Website (e.g., Facebook, Twitter)**

* While still on the **password reset page**, **click on a link** on that page (e.g., Facebook/Twitter icon).
* The idea is that if the site includes social media links or redirects to another site, your browser will send a **Referer header** to that site.



###  **4. Intercept the Request in Burp Suite**

* Make sure **Burp Suite Proxy** is running and your browser is configured to send traffic through it.
* When you click the external link (Facebook/Twitter/etc.), **intercept the HTTP request** using Burp.



###  **5. Check the `Referer` Header**

* In Burp Suite, inspect the intercepted request.
* Look at the `Referer:` header. Example:

  ```
  Referer: https://example.com/reset-password?token=abc123
  ```
* If the token appears here, **it's a vulnerability**. You're leaking a **sensitive reset token** to a third party.



##  Why This Matters

* If a malicious third-party site gets this token, they can reset the user's password without their consent.
* This is a **high-risk vulnerability** in real-world apps.



##  Recommendations (for developers/security team)

* Avoid including sensitive tokens in URLs — use POST requests or session-based flows.
* Use **Referrer-Policy headers**, e.g.:

  ```http
  Referrer-Policy: no-referrer
  ```

  or

  ```http
  Referrer-Policy: strict-origin
  ```
