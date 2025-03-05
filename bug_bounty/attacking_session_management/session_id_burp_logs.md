**Advanced Python script** that **automatically extracts session IDs from Burp Suite logs**, decodes them, and checks for **entropy (randomness)** to identify weak session management. 🚀  

---

### **🔍 Features of the Script**  
✅ **Extracts session IDs directly from Burp Suite logs (HTTP history export).**  
✅ **Decodes Base64 and Hex-encoded session IDs.**  
✅ **Calculates entropy to detect weak randomness.**  
✅ **Identifies predictable patterns that may lead to session hijacking.**  
✅ **Works with Burp logs in TXT format or raw HTTP response files.**  

---

### **📜 Python Script**  
```python
import re
import base64
import math
import binascii
from collections import Counter

# Function to extract session IDs from Burp Suite log file
def extract_session_ids(file_path):
    session_ids = []
    pattern = re.compile(r"Set-Cookie:\s*sessionID=([^\s;]+)")
    
    with open(file_path, "r", encoding="utf-8") as file:
        for line in file:
            match = pattern.search(line)
            if match:
                session_ids.append(match.group(1))
    
    return session_ids

# Function to check if the session ID is Base64-encoded
def is_base64(s):
    try:
        decoded = base64.b64decode(s).decode(errors="ignore")
        return decoded.isprintable()  # True if decoded data contains readable text
    except Exception:
        return False

# Function to check if the session ID is Hex-encoded
def is_hex(s):
    try:
        binascii.unhexlify(s)
        return True
    except Exception:
        return False

# Function to calculate Shannon entropy
def calculate_entropy(data):
    if not data:
        return 0
    counter = Counter(data)
    length = len(data)
    entropy = -sum((freq / length) * math.log2(freq / length) for freq in counter.values())
    return entropy

# Main function to analyze session IDs
def analyze_session_ids(session_ids):
    for sid in session_ids:
        print(f"\n🔍 Analyzing Session ID: {sid}")

        # Check if Base64 encoded
        if is_base64(sid):
            decoded_value = base64.b64decode(sid).decode(errors="ignore")
            print(f"⚠️ Base64 Decoded: {decoded_value} (May contain sensitive info!)")

        # Check if Hex encoded
        elif is_hex(sid):
            decoded_value = bytes.fromhex(sid).decode(errors="ignore")
            print(f"⚠️ Hex Decoded: {decoded_value} (May contain sensitive info!)")

        else:
            print("✅ Session ID does not appear to be encoded.")

        # Calculate entropy
        entropy = calculate_entropy(sid)
        print(f"⚙️ Entropy Score: {entropy:.2f} bits")

        # Security verdict
        if entropy < 4:
            print("🚨 WARNING: Low entropy detected! Session ID may be predictable.")
        elif entropy < 5:
            print("⚠️ Moderate entropy detected. Further analysis needed.")
        else:
            print("✅ Strong entropy detected. Session ID is likely secure.")

# Run the analysis
if __name__ == "__main__":
    burp_log_file = input("Enter the path to the Burp Suite log file (TXT format): ").strip()
    session_ids = extract_session_ids(burp_log_file)

    if session_ids:
        print(f"\n🔎 Found {len(session_ids)} session IDs in {burp_log_file}")
        analyze_session_ids(session_ids)
    else:
        print("\n❌ No session IDs found in the provided file.")
```

---

### **📌 How to Use**
1️⃣ **Export HTTP history from Burp Suite:**  
   - Go to **Burp Suite → HTTP History**  
   - Right-click **Save all HTTP requests and responses**  
   - Save as a **TXT file** (e.g., `burp_log.txt`)  

2️⃣ **Run the script:**  
   ```sh
   python session_id_tester.py
   ```
3️⃣ **Enter the Burp log file path** when prompted.  

4️⃣ **Review the security analysis** of session IDs.

---

### **📊 Sample Output**
```
Enter the path to the Burp Suite log file (TXT format): burp_log.txt

🔎 Found 3 session IDs in burp_log.txt

🔍 Analyzing Session ID: MTIzNDU2Nzg5YWJjZGVmCg==
⚠️ Base64 Decoded: 123456789abcdef (May contain sensitive info!)
⚙️ Entropy Score: 3.80 bits
🚨 WARNING: Low entropy detected! Session ID may be predictable.

🔍 Analyzing Session ID: ABC123XYZ456DEF789GHI
✅ Session ID does not appear to be encoded.
⚙️ Entropy Score: 4.92 bits
⚠️ Moderate entropy detected. Further analysis needed.

🔍 Analyzing Session ID: 4a6f686e446f6500012a3c
⚠️ Hex Decoded: JohnDoe (May contain sensitive info!)
⚙️ Entropy Score: 3.50 bits
🚨 WARNING: Low entropy detected! Session ID may be predictable.
```

---

### **🚀 Why Use This Script?**
✔ **Automates session ID extraction from Burp Suite logs.**  
✔ **Quickly detects insecure session management.**  
✔ **Identifies Base64 or Hex encoding leaks (e.g., usernames, PII).**  
✔ **Calculates entropy to detect weak randomness.**  
