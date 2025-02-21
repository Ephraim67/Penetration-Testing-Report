This section discusses different attack strategies when dealing with **opaque (obfuscated or encrypted) data** being transmitted between the client and server. Let’s go through each approach with **real-world examples** that align with **bug bounty testing**.

---

### **Example Scenario: E-commerce Website with Encrypted Pricing Tokens**
Imagine you're testing an online store that uses a **pricing token** in purchase requests to prevent users from manually modifying product prices.

When you add an item to your cart, you see a request like:

```
POST /checkout HTTP/1.1
Host: shop.com
Content-Type: application/json

{
  "product_id": 1234,
  "pricing_token": "XyZ12AbCdEfG"
}
```

---

### **Step 1: Decipher the Obfuscation Algorithm**
- If you **already know the plaintext price**, you can try reversing the encoding.
- Example: You buy a **$50 product** and receive `pricing_token="XyZ12AbCdEfG"`, then buy a **$100 product** and receive `pricing_token="MnOp34GhIjKl"`.
- By analyzing multiple transactions, you might identify **a weak encoding pattern** (e.g., Base64, XOR, ROT13).
- If you successfully reverse-engineer it, you could **generate a pricing token for an arbitrary price** (e.g., $1).

**Bug Example:**  
A researcher found that a **discount code system** used **Base64 encoding**, so they could easily decode and modify the discount value.

---

### **Step 2: Find an On-Site Function that Generates the Opaque String**
- Sometimes, the **application itself provides a way** to generate valid opaque tokens.
- Example:  
  - A **coupon code generation API** may let users create pricing tokens.
  - If you input a value like `price=1`, the API might return the **encrypted equivalent**, which you could use in an actual checkout.

**Bug Example:**  
A researcher on **HackerOne** found a **JWT token generation endpoint** that allowed them to create an **admin token** by modifying parameters before signing.

---

### **Step 3: Replay the Opaque Token in Another Context**
- Even if you **can’t decrypt** the pricing token, you might be able to **reuse one from a cheaper product**.
- Example:
  1. Add **Product A ($10)** to your cart.
  2. Extract the **pricing_token**.
  3. Remove Product A and add **Product B ($100)**.
  4. Replace Product B’s pricing_token with **Product A’s token**.
  5. Submit the checkout request.

- If the server blindly trusts the token, you’ll get **Product B for $10**.

**Bug Example:**  
A researcher found that an **airline ticketing system** allowed copying **a promo code's encrypted value** and applying it to **any flight**, even first-class.

---

### **Step 4: Submit Malformed Tokens to Break Server Logic**
- If the system **decrypts or validates** the token server-side, try:
  - **Truncating** the token (`XyZ12AbCdEfG` → `XyZ12`)
  - **Adding extra characters** (`XyZ12AbCdEfG!!!`)
  - **Flipping byte orders**
  - **Injecting special characters** (`' OR 1=1 --`)
- If the application **crashes, leaks errors, or accepts invalid data**, you may have found:
  - **A cryptographic flaw**
  - **An injection point**
  - **Broken authentication logic**

**Bug Example:**  
A researcher submitted **malformed JWT tokens** to a banking app, which **bypassed authentication** due to improper error handling.

---

### **Takeaways**
- Look for **pricing tokens, authentication tokens, and encrypted values**.
- Try **replaying, modifying, or tampering** with these values.
- Check if Deriv has **an endpoint that generates tokens** you can abuse.
- Submit **malformed tokens** to test decryption logic.
