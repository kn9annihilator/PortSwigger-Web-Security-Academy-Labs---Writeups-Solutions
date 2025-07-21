When designing APIs, integrate security from the start. Key recommendations include:

    -Restrict access to API documentation if the API is not public.

    -Keep documentation up to date to provide testers with complete visibility of the API surface.

    -Implement an allowlist for permitted HTTP methods.

    -Validate content types for all requests and responses.

    -Use generic error messages to prevent information leakage.

    -Apply security measures across all API versions, not only the current production release.

    -Prevent mass assignment vulnerabilities by allowlisting user-updatable properties and blocklisting sensitive fields.