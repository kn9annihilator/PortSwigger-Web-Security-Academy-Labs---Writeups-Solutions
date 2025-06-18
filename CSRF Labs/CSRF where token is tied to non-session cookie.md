# 🛡️ CSRF – Cookie Injection via Reflected Input (csrfKey Manipulation)

## 🎯 Objective

Exploit a CSRF vulnerability where the `csrfKey` is not tied to the session, allowing injection via a reflected `Set-Cookie` header and triggering unauthorized email change.

---

## 🧪 Steps Followed

1. **Login using Burp's browser**, submit the "Update email" form, and find the request in **Proxy → HTTP history**.

2. 📤 **Send the request to Repeater**. Change:
   - The `session` cookie → logs out the user.
   - The `csrfKey` cookie → request is rejected, but **session remains**.

   ✅ This suggests `csrfKey` is **not strictly tied to session**.

3. 🔐 Open **incognito mode**, login to the **other user account**, and submit an email change request.

4. 🔁 In Burp Repeater, replace:
   - The `csrfKey` cookie
   - The CSRF form parameter
   ...with values from the **first account**.

   ✅ The request succeeds → proving **cross-account CSRF** using injected cookies.

---

## 💣 Exploiting Cookie Injection via Search

5. Back in the original browser:
   - Perform a **search**.
   - Send the request to Repeater.
   - Observe the **Set-Cookie header reflects the search term**.

   ✅ This is a **reflected input in header**, making it vulnerable to **HTTP header injection**.

---

## 🔗 Constructing Exploit URL

Use the search functionality to inject your `csrfKey` into the victim’s browser:

```text
https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None
