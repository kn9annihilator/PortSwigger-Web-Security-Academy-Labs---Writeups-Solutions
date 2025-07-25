# Testing for Server-Side Parameter Pollution (SSPP) in Query Strings

Server-Side Parameter Pollution (SSPP) vulnerabilities arise when an application accepts multiple parameters with the same name or allows special characters to alter how parameters are interpreted by the server. Attackers can exploit this behavior to manipulate backend behavior, bypass security controls, or gain unauthorized access to sensitive information.

This document outlines methods for detecting and exploiting SSPP through the query string.

---

## Scenario Overview

Consider a vulnerable application that allows users to search for other users by their username. When a search is performed, the browser sends the following request:

```sql
GET /userSearch?name=peter&back=/home
```

Internally, the server sends a request to another API endpoint:

```sql
GET /users/search?name=peter&publicProfile=true
```

An attacker may attempt to manipulate this request through various techniques outlined below.

---

## Truncating Query Strings

One technique involves attempting to truncate the server-side request using a URL-encoded `#` character (`%23`). Since `#` starts a fragment identifier in URLs, if it reaches the server-side API unfiltered, it may cause the backend to ignore the rest of the query string.

### Example

Request:
```sql
GET /userSearch?name=peter%23foo&back=/home
```


Resulting server-side request:
```sql
GET /users/search?name=peter#foo&publicProfile=true
```

### Interpretation

- If the backend returns results for the user `peter`, truncation may have occurred, ignoring `publicProfile=true`.
- If the application returns an error like `Invalid name`, the fragment (`foo`) may be treated as part of the `name` value, and truncation likely failed.

This technique is useful for bypassing enforced query parameters such as `publicProfile=true`.

---

## Injecting Invalid Parameters

An attacker can try injecting additional parameters using a URL-encoded ampersand (`%26`) to see how the server parses them.

### Example

Request:

```sql
GET /userSearch?name=peter%26foo=xyz&back=/home
```

Resulting server-side request:

```sql
GET /users/search?name=peter&foo=xyz&publicProfile=true
```



### Interpretation

- If the response is unchanged, the `foo` parameter may have been injected but ignored.
- If the application returns a different result or error, this may indicate that the parameter is processed by the backend.

This approach helps in probing how flexible the backend is in accepting arbitrary or hidden parameters.

---

## Injecting Valid Parameters

If you know or suspect the presence of other valid backend parameters (e.g., `email`), you can attempt to inject them to influence server behavior.

### Example

Request:

```sql
GET /userSearch?name=peter%26email=foo@example.com&back=/home
```

Resulting server-side request:

```sql
GET /users/search?name=peter&email=foo@example.com&publicProfile=true
```

### Interpretation

- If the application returns details for the user associated with `foo@example.com`, then the parameter injection has been successful.
- This can be used to gain unauthorized access to sensitive data or perform horizontal privilege escalation.

---

## Overriding Existing Parameters

You can also try injecting a duplicate parameter to override the original value. The effect depends on how the backend server parses multiple parameters with the same name.

### Example

Request:

```sql
GET /userSearch?name=peter%26name=carlos&back=/home
```

Resulting server-side request:

```sql
GET /users/search?name=peter&name=carlos&publicProfile=true
```


### Behavior by Technology

- **PHP**: Uses the last parameter (`name=carlos`) → retrieves `carlos`.
- **ASP.NET**: Combines parameters (`name=peter,carlos`) → may throw an error or behave unexpectedly.
- **Node.js/Express**: Uses the first parameter (`name=peter`) → ignores override.

### Exploitation Example

If you're able to override the parameter, you could potentially impersonate another user:

```sql
GET /userSearch?name=peter%26name=administrator&back=/home
```


If the backend accepts the last value, this may result in accessing the administrator's profile.

---

## Tips for Effective Testing

- Always URL-encode special characters like `#` (`%23`) and `&` (`%26`).
- Use application error messages and behavioral changes as clues.
- Chain these techniques with other vulnerabilities (e.g., IDOR, broken access controls) for deeper exploitation.

---

## Related Topics

- Hidden Parameters Discovery
- Server-Side Request Forgery (SSRF)
- HTTP Parameter Pollution (HPP)
- Insecure Direct Object References (IDOR)

---

## References

- [OWASP Server-Side Parameter Pollution](https://owasp.org/www-community/attacks/Server_Side_Parameter_Pollution)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

   


### AMI Types Comparison

| **AMI Type**        | **Description**                                               | **Best For**                            |
|---------------------|---------------------------------------------------------------|------------------------------------------|
| **Public AMIs**     | Provided by AWS or the community, accessible by all users     | Basic OS setups, public reference builds |
| **Private AMIs**    | Visible only to the AWS account that created them             | Internal applications, staging builds    |
| **Shared AMIs**     | Shared with specific AWS accounts or AWS Organizations        | Cross-account deployments                |
| **Marketplace AMIs**| Distributed by vendors via AWS Marketplace with licensing     | Enterprise software, firewalls, databases|
