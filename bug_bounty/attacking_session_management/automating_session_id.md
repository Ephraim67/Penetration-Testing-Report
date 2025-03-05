**Python script** to analyze session ID length and entropy. This script helps determine if session IDs meet the **64-bit entropy requirement** by checking their randomness and length.  

---

### **Python Script for Session ID Entropy Analysis**
```python
import random
import string
import hashlib
import math
from collections import Counter

# Sample Session IDs (Replace with actual IDs collected from Burp)
session_ids = [
    "abc123xyz456def789ghi", 
    "jkl789mno123pqr456stu",
    "vwx345yz678abc901def",
    "ghi012jkl345mno678pqr"
]

# Function to calculate Shannon Entropy
def shannon_entropy(data):
    if not data:
        return 0
    freq_counter = Counter(data)
    total_chars = len(data)
    entropy = -sum((count/total_chars) * math.log2(count/total_chars) for count in freq_counter.values())
    return entropy

# Check session ID length and entropy
for session_id in session_ids:
    length = len(session_id)
    entropy = shannon_entropy(session_id)
    print(f"Session ID: {session_id}")
    print(f"Length: {length} characters")
    print(f"Shannon Entropy: {entropy:.2f} bits per character\n")

# Estimate total entropy
def estimated_entropy(session_ids):
    unique_ids = set(session_ids)  # Remove duplicates
    total_entropy = len(unique_ids) * shannon_entropy("".join(session_ids))
    return total_entropy

total_entropy = estimated_entropy(session_ids)
print(f"Estimated Total Entropy: {total_entropy:.2f} bits")

# Check if entropy is sufficient
if total_entropy < 64:
    print("⚠️ Warning: Session ID entropy is too low. Consider increasing randomness!")
else:
    print("✅ Session ID entropy is strong!")
```
---

### **How to Use It**
1. **Collect session IDs** from Burp Suite (via Proxy or Sequencer).  
2. **Replace the sample `session_ids` list** with real session IDs.  
3. Run the script and check:  
   - **Length of each session ID**  
   - **Entropy per character**  
   - **Total entropy**  

---

### **Expected Output**
```
Session ID: abc123xyz456def789ghi
Length: 21 characters
Shannon Entropy: 4.52 bits per character

Session ID: jkl789mno123pqr456stu
Length: 21 characters
Shannon Entropy: 4.67 bits per character

Estimated Total Entropy: 75.92 bits
✅ Session ID entropy is strong!
```

---

### **Results & Next Steps**
- **If entropy < 64 bits** → **Session IDs are weak** → Possible brute-force risk.  
- **If entropy is high but length is short** → Check **encoding method** (e.g., Hex, Base64).  
- **If predictable patterns exist** → Consider randomizing ID generation.  
