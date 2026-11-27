# Access Control

## What is access control

Access control decides who can access a resource or perform an action.

In web apps:

- Authentication checks who you are
- Session management keeps track of your requests
- Access control checks what you are allowed to do

Broken access control means a user can access something they should not.

## Access control models

### Programmatic access control
- Permissions are stored in a database or similar place
- The app checks the permission list when a user makes a request
- Can be very detailed

### DAC
Discretionary Access Control

- Resource owner can give or remove permissions
- Access can be set for users or groups
- Flexible but can get messy to manage

### MAC
Mandatory Access Control

- Access is controlled centrally
- Users and resource owners cannot change or share permissions themselves
- Often used in military or clearance-based systems

### RBAC
Role-Based Access Control

- Permissions are given to roles
- Users get one or more roles
- Example: a purchase clerk role can access purchase records
- Easier to manage when people join or leave
- Too many roles can make it complicated

## Types of access control

### Vertical access control
Controls which type of user can access certain features.

Example:
- Admin can delete accounts
- Normal users cannot

### Horizontal access control
Controls which user's resources can be accessed.

Example:
- A user can see their own bank transactions
- They cannot see another user's transactions

### Context-dependent access control
Access depends on the app's current state or the order of actions.

Example:
- A shopping cart cannot be changed after payment

## Privilege escalation

Privilege escalation happens when a user gets access they should not have.

### Vertical privilege escalation
A normal user gets higher-level access, such as admin functions.

Example:
- A normal user can open an admin page and delete accounts

### Unprotected admin pages
Sometimes an admin page is only hidden from the normal user interface.

But hiding a link is not access control.

A user may try the URL directly, for example:

```text
/admin
```

Admin URLs may also leak through:
- `robots.txt`
- JavaScript files
- other pages

A wordlist may also help discover hidden paths.

### Security by obscurity
Using a hard-to-guess admin URL is not enough.

If the URL leaks or gets discovered, the page is still unprotected. Real permission checks are needed.

## Parameter-based access control

Some apps store the user's role or permission in a place the user can change.

Examples:

- Hidden form field
- Cookie
- URL parameter

Example URLs:

```text
/login/home.jsp?admin=true
/login/home.jsp?role=1
```

This is insecure if the server trusts the value without checking it.

A user might change a cookie like:

```text
Admin=true
```

The server should verify permissions on its side, not trust user-controlled values.

## Burp Repeater notes

If you change a cookie in Burp Repeater and get `200 OK`, it does not mean the browser session has changed.

Repeater sends its own request and shows the response inside Burp.

`200 OK` only means the server returned a successful HTTP response. It does not automatically mean:
- You became an admin
- The response contains the admin panel
- Your browser cookie was updated
- The browser loaded the response

### Check the admin page in Repeater

For a lab where the admin page is at `/admin`:

1. Change the request path to:

```http
GET /admin HTTP/2
```

2. Keep the lab session cookie and the modified cookie:

```http
Cookie: session=YOUR_SESSION; Admin=true
```

3. Send the request
4. Check the response body

If access worked, the response may show an Admin panel or user delete links.

### Make it work in the browser

Repeater does not update browser cookies.

In Firefox:

1. Open the lab
2. Press `F12`
3. Open the Storage tab
4. Select Cookies for the lab domain
5. Add or edit the cookie

```text
Name: Admin
Value: true
```

6. Refresh the page
7. Visit `/admin`

If the Storage tab is not available, use Firefox Web Developer Tools or Burp's built-in browser.

### Change the cookie through Burp Proxy

1. Open Burp Proxy
2. Turn Intercept on
3. Visit `/admin` in the browser
4. When the request is intercepted, change the cookie to:

```text
Admin=true
```

5. Forward the request
6. Check the page in the browser


---

# Lab: User role can be modified in user profile

**Difficulty:** Apprentice  
**Status:** Not solved

## Lab description

The lab has an admin panel at `/admin`.

Only logged-in users with a `roleid` of `2` can access it.

Goal: Access the admin panel and delete the user `carlos`.

Credentials:

```text
Username: wiener
Password: peter
```
## Solution

1. Logged in using `wiener:peter`
2. Went to my account and updated my email
3. Intercepted the email update request in Burp Repeater
4. Added `"roleid":2` to the JSON body and resent the request
5. The server accepted the role change
6. Opened `/admin` and deleted `carlos`

---

# Lab: URL-based access control can be circumvented

**Difficulty:** Practitioner  
**Status:** Not solved

## Solution

1. Tried to open `/admin` and got blocked by the front-end.
2. Sent the request to Burp Repeater, changed the URL to `/` and added `X-Original-URL: /invalid`.
3. Got a not found response, confirming the back-end was using the `X-Original-URL` header.
4. Changed the header to `X-Original-URL: /admin` and accessed the admin panel.
5. Added `?username=carlos` to the actual query string and changed the header to `X-Original-URL: /admin/delete`.
6. Deleted `carlos`.



# User ID controlled by request parameter

**Difficulty:** Apprentice  
**Status:** Not solved

## Solution

1. Logged in using `wiener:peter` and opened my account page.
2. Noticed the URL had my username in the `id` parameter.
3. Sent the request to Burp Repeater.
4. Changed the `id` parameter to `carlos`.
5. Retrieved and submitted Carlos's API key.

---

# User ID controlled by request parameter, with unpredictable user IDs

**Difficulty:** Apprentice  
**Status:** Solved

## Solution

1. Found a blog post by Carlos.
2. Clicked his name and noted the user ID from the URL.
3. Logged in using `wiener:peter` and opened my account page.
4. Replaced my `id` parameter with Carlos's user ID.
5. Retrieved and submitted his API key.

---

# User ID controlled by request parameter with data leakage in redirect

**Difficulty:** Apprentice  
**Status:** Not solved

## Solution

1. Logged in using `wiener:peter` and opened my account page.
2. Sent the request to Burp Repeater.
3. Changed the `id` parameter to `carlos`.
4. The response redirected to the home page, but the response body still contained Carlos's API key.
5. Submitted the API key.
