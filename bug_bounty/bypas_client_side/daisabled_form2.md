### **🔍 Exploiting Disabled Form Elements in Web Applications**  

**Goal:** Identify and exploit cases where **disabled form elements** can be submitted manually, bypassing client-side restrictions.  

---

## **🚀 Steps to Exploit Disabled Form Elements**  

### **1️⃣ Identify Disabled Form Elements**
- Open the **web application** and **inspect** each form’s HTML.  
- Look for elements with the **`disabled`** attribute in **inputs, buttons, or selects**.  
- Example:  
  ```html
  <input type="text" name="discount_code" value="DISABLED" disabled>
  <button type="submit" disabled>Submit</button>
  ```
- Browsers **do not** submit disabled elements when forms are sent, so you must manually modify the request.

---

### **2️⃣ Submit Disabled Elements Manually**
- Since browsers exclude disabled elements from form submissions, try:  
  - **Editing the HTML manually** in DevTools (`F12` → `Elements` tab).  
  - **Intercepting the request using Burp Suite** and adding the missing field.  
  - **Crafting a manual HTTP request** using `cURL` or Python (`requests`).  

---

### **3️⃣ Common Exploits of Disabled Elements**
🔹 **Hidden Discounts & Pricing Manipulation**
   - A disabled field might hold **price discounts, coupons, or promo codes** that the user isn’t supposed to modify.  
   - **Exploit:** Manually enable it and submit **custom discount values**.  
   - Example:
     ```html
     <input type="text" name="discount_code" value="SAVE50" disabled>
     ```
     - **Change in Burp Suite**:  
       ```
       discount_code=SAVE90
       ```
     - If accepted by the server, you get **a larger discount than allowed**! 🎉  

🔹 **Privilege Escalation via Disabled Buttons**
   - Some admin-only actions may have **disabled submit buttons**.  
   - **Exploit:** Enable the button and submit the form.  
   - Example:
     ```html
     <button type="submit" disabled>Delete User</button>
     ```
     - **Modify HTML:**
       ```html
       <button type="submit">Delete User</button>
       ```
     - If no server-side validation exists, you can **delete users without permission**.  

🔹 **Editing Disabled Fields in User Profiles**
   - Some sites disable **email, username, or account type** fields.  
   - **Exploit:** Enable them and modify data (e.g., change email to an admin's email for account takeover).  
   - Example:
     ```html
     <input type="text" name="email" value="user@example.com" disabled>
     ```
     - **Modify & Submit:**
       ```
       email=admin@example.com
       ```
     - If accepted, you can **reset the admin's password**! 🚀  

---

### **4️⃣ Finding Disabled Elements with Python (Automation)**
Use **BeautifulSoup** to **automate the detection of disabled fields** in web forms.

#### **📝 Python Script to Find Disabled Form Elements**
```python
import requests
from bs4 import BeautifulSoup

# Target URL
URL = "https://example.com/profile"

# Headers (to mimic a real browser)
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
}

def find_disabled_elements(url):
    """Finds all disabled form elements in a web page."""
    response = requests.get(url, headers=HEADERS)
    soup = BeautifulSoup(response.text, "html.parser")

    disabled_fields = []
    
    # Find all disabled input elements
    for input_tag in soup.find_all("input", {"disabled": True}):
        name = input_tag.get("name")
        value = input_tag.get("value", "")
        disabled_fields.append((name, value))

    if disabled_fields:
        print("\n[!!!] Disabled Form Elements Found:")
        for name, value in disabled_fields:
            print(f" - {name}: {value}")
    else:
        print("\n[✔] No disabled elements found.")

if __name__ == "__main__":
    find_disabled_elements(URL)
```

---

## **🔐 Defense Against This Attack**
✅ **Always validate all input on the server** (do not rely on client-side validation).  
✅ **Use proper authentication & authorization checks** (e.g., only admins can perform actions).  
✅ **Never trust client-side disabled fields** (use backend verification).  
✅ **Use hidden fields with HMAC signing** to detect tampering.  

---

## **🔍 Conclusion**
🔸 **Disabled elements are only enforced client-side**, making them easy to **manually enable and exploit**.  
🔸 Using **Burp Suite, DevTools, or Python scripts**, attackers can **modify and submit these fields** to gain unauthorized privileges, discounts, or exploit security flaws.  
🔸 Developers **must enforce validation on the server** to prevent such attacks.  
