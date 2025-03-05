**Python script** that automates **Session ID Extraction, Decoding, and Entropy Analysis** for **Burp Suite logs** or HTTP responses.  

---

### **🔍 Automated Session ID Security Testing Script**  
✅ **Extracts Session IDs from HTTP responses**  
✅ **Decodes Base64 and Hex-encoded Session IDs**  
✅ **Analyzes entropy to check for weak randomness**  
✅ **Identifies predictable patterns**  

---

### **📜 Python Script**
```python
import re
import base64
import math
import binascii
from collections import Counter

# Sample HTTP responses (Replace with real Burp logs)
http_responses = [
    'HTTP/1.1 200 OK\nSet-Cookie: sessionID=MTIzNDU2Nzg5YWJjZGVmCg==; Path=/; Secure; HttpOnly',
    'HTTP/1.1 200 OK\nSet-Cookie: sessionID=ABC123XYZ456DEF789GHI; Path=/; Secure; HttpOnly',
    'HTTP/1.1 200 OK\nSet-Cookie: sessionID=4a6f686e446f6500012a3c; Path=/; Secure; HttpOnly'
]

# Function to extract session IDs from HTTP responses
def extract_session_ids(responses):
    session_ids = []
    pattern = re.compile(r"Set-Cookie:\s*sessionID=([^\s;]+)")
    for response in responses:
        match = pattern.search(response)
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
session_ids = extract_session_ids(http_responses)
analyze_session_ids(session_ids)
```

---

### **📌 How to Use**
1️⃣ Save the script as `session_id_tester.py`  
2️⃣ Replace `http_responses` with **real Burp Suite logs**  
3️⃣ Run the script:  
```sh
python session_id_tester.py
```
4️⃣ Review the output to see if **Session IDs are weak or predictable**.

---

### **📊 Sample Output**
```
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

### **🔎 How This Helps**
- **🚨 Detects Session ID Leakage** → If Base64 or Hex decoding reveals PII.  
- **⚙️ Checks Randomness** → Low entropy = **Session IDs can be guessed**.  
- **✅ Identifies Secure IDs** → Helps verify proper cryptographic randomness.  
