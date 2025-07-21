# Server-Side Parameter Pollution (SSPP)
Server-side parameter pollution (SSPP) occurs when unvalidated user input is embedded into server-side requests—often to internal APIs—without proper sanitization or encoding. This can allow attackers to manipulate request parameters in ways that bypass logic or gain unauthorized access.

## Potential Impact
An attacker may exploit SSPP to:

-Override existing parameters.

-Alter application logic or behavior.

-Bypass access controls.

-Access or manipulate unauthorized data.

## Common Injection Vectors

You should test all user-controlled input sources, including:

-Query string parameters

-Form fields

-HTTP headers

-URL path parameters

-Cookies

Attackers may inject duplicate parameters or unexpected encodings to trigger unintended behavior on the server side.
## Example

### Consider a server-side request like:
```bash
GET /internal-api/user?role=user
```

### If an attacker can inject a second role parameter:
```bash
GET /internal-api/user?role=user&role=admin
```

Depending on how the backend handles duplicated parameters, this might escalate privileges.

## WAF Bypassing via Parameter Pollution
Web Application Firewalls (WAFs) often rely on pattern-based detection to block malicious inputs. Parameter pollution can be used to bypass these filters by:

-Duplicating parameters (e.g., role=admin&role=user)

-Using non-standard encodings (e.g., role%00=admin)

-Modifying parameter names (e.g., ro%6Ce=admin)

-Using alternate HTTP methods (e.g., POST with _method=GET)

-Nesting parameters in unexpected formats (e.g., arrays or object notation)

Some WAFs may only inspect the first occurrence of a parameter or fail to normalize encoded values correctly. Attackers can exploit these inconsistencies to evade detection.
### Important Notes

    Not to be confused with HTTP Parameter Pollution (HPP): While the terms are sometimes used interchangeably, HPP usually refers to client-side or WAF-focused bypass techniques, whereas SSPP refers specifically to server-side vulnerabilities in how internal APIs process parameters.

    Different from Prototype Pollution: SSPP is unrelated to server-side prototype pollution, which involves manipulating JavaScript object prototypes.