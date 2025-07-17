Mass Assignment: Feature to Flaw — With Request and Response Examples

1. Mass Assignment as a Feature

    Request to update user info:
```python
PATCH /api/users/123
Content-Type: application/json


{
  "username": "newuser",
  "email": "newuser@example.com"
}
```

    Server-side action (intended):

The framework automatically maps these fields to the user object:
```python
user.update({
  "username": "newuser",
  "email": "newuser@example.com"
})
```
    Response:
```c
HTTP/1.1 200 OK
{
  "id": 123,
  "username": "newuser",
  "email": "newuser@example.com",
  "isAdmin": false
}
```

2. Mass Assignment as a Flaw

    Malicious request adding extra fields:
```c
PATCH /api/users/123
Content-Type: application/json

{
  "username": "attacker",
  "isAdmin": true
}
```
    If no filtering is done, the framework blindly maps:
```c
user.update({
  "username": "attacker",
  "isAdmin": true  # Sensitive field accidentally updated
})
```
    Resulting response:
```c
HTTP/1.1 200 OK
{
  "id": 123,
  "username": "attacker",
  "email": "victim@example.com",
  "isAdmin": true  # Privilege escalated!
}
```
3. Why This Happens

    The API does not validate or restrict which fields can be updated.

    Framework mass assignment features treat all fields equally.

4. How to Fix

    Use a whitelist to only allow permitted fields:
```c
user.update(request.body.only(['username', 'email']))
```
    Reject or ignore fields like isAdmin from user input.