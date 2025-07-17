# Fuzzing API Endpoints with Burp Intruder

1. What is Fuzzing?

Fuzzing sends many varied inputs to an API to find hidden endpoints, unexpected behavior, or security issues.
2. Example Scenario: Known Endpoint

You have this API endpoint:
```c
PUT /api/user/update
```

Your goal is to find other related endpoints like 
```c 
/api/user/delete, /api/user/add, /api/user/reset-password, etc.
```
3. Using Burp Intruder for Path Fuzzing
Step 1: Capture a baseline request in Burp Proxy
```c
PUT /api/user/update HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer abc123

{
  "username": "alice"
}
```

Step 2: Send the request to Intruder

Right-click the request → "Send to Intruder"


Step 3: Configure Intruder positions

Select just the word update in the path as the fuzzing position 
```python
    (highlight update and click “Add §”).
```

Your target request path now looks like:

```python
PUT /api/user/§update§
```

Step 4: Load payloads (wordlist)

Use a wordlist containing common API actions, for example:

delete
add
reset-password
admin
create
list

Step 5: Start the attack

    Intruder will substitute each payload word into the fuzz position.

    Example requests sent:
```c
PUT /api/user/delete
PUT /api/user/add
PUT /api/user/reset-password
```

4. Analyzing Responses

Look for:
```json
    Status codes: 200, 201, 204 may indicate valid endpoints.

    Response length: Different length or content may hint at endpoint behavior.

    Response body: Unexpected fields or error messages.
```

Example:
```yaml
Request URL	Status	Response Body
/api/user/update	200	{"success": true}
/api/user/delete	403	{"error":"Forbidden"}
/api/user/reset-password	200	{"message":"Password reset link sent"}
/api/user/admin	404	{"error":"Not Found"}
```

From this, /reset-password is a valid, functional endpoint.


5. HTTP Method Fuzzing Example

Try different methods on the same endpoint:
```yaml
GET /api/user/reset-password
POST /api/user/reset-password
PUT /api/user/reset-password
DELETE /api/user/reset-password
```

Some may return 405 (Method Not Allowed), others may perform different actions.

6. Why This Matters

    Hidden or undocumented endpoints may expose sensitive functionality.

    Knowing valid endpoints helps further security testing, like parameter fuzzing or authentication bypass.

7. Summary of Burp Intruder Use for Endpoint Fuzzing


| STEP              | ACTION                                                    |
| ----------------- | --------------------------------------------------------- |
| Capture requesT   | Proxy → Capture the known API request                     |
| Send to IntrudeR  | Right-click → Send to Intruder                            |
| Select position   | Highlight part of path to fuzz (e.g., update)             |
| Load payloads     | Load a wordlist with common endpoint names                |
| Start attack      | Run Intruder to try different endpoint variations         |
| Analyze responses | Look for status codes, content changes, or error messages |