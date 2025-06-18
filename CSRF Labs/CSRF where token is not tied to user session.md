# 🛡️ CSRF – Exploiting a Same-Site Attack with Single-Use Token

## 🎯 Objective

Exploit a CSRF vulnerability where tokens are single-use, but can be stolen from one user and used against another.

---

## 🧪 Steps Followed

1. ✅ **Login using Burp’s browser** and navigate to the "Update email" form.
2. 🎯 **Intercept the email update request** in Burp and note the value of the CSRF token.
3. 🗑️ **Drop the request** without sending it to the server.
4. 🕵️ **Open an incognito/private window**, log in to the **other user account**, and send the earlier intercepted request using **Burp Repeater**.
5. 🔁 **Swap the CSRF token** in the request with the one copied from the first user.
6. 📥 **Observe that the request is accepted**, confirming the CSRF vulnerability.

---

## 💣 Creating the Exploit

- 💻 **Host a PoC exploit page** similar to the one used in the "CSRF with no defenses" lab.
- 🔄 Use the fresh CSRF token (from step 2) in the exploit.
- ✏️ Change the email address in the payload to something **other than your own**.
- 💾 Store the exploit page.
- 📦 Click **“Deliver to victim”** to complete the lab.

---

## ✅ Lab Solved

- The server accepted a CSRF token stolen from another session.
- A CSRF attack was successfully executed even with single-use tokens due to session mismanagement.

---

## 🧠 Key Takeaways

- CSRF tokens must not be predictable or reusable across users.
- Even single-use tokens can be vulnerable if they are tied to user sessions insecurely.
- Exploit scenarios can still work by forcing session context from one user to another.

