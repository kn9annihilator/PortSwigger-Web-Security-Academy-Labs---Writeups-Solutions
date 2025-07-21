# Server-Side Parameter Pollution (SSPP)
Server-side parameter pollution (SSPP) occurs when unvalidated user input is embedded into server-side requests—often to internal APIs—without proper sanitization or encoding. This can allow attackers to manipulate request parameters in ways that bypass logic or gain unauthorized access.

## Potential Impact

An attacker may exploit SSPP to:

-Override existing parameters.

-Alter application logic or behavior.

-Bypass access controls.

-Access or manipulate unauthorized data.