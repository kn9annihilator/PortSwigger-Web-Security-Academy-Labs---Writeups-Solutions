Documentation is done so that developers are able to read it and know how to use them and integrate them.
It includes detailed explanations, examples and usage scenarios. Machine-readable documentation is designed to be processed by oftware for automating taks like API integration and validation.
Possible file formats are JSON and XML

Recon must always start bby reading an API's documentation(if publically available)


>>>Discovering API Documentation

Use Burp Scanner to crawl the API
Look for endpoints that may refer to API documentation, for example:

    /api
    /swagger/index.html
    /openapi.json

🔸 1. /api/swagger/v1/users/123

    This likely targets a specific user (ID: 123).

    Check if the ID can be iterated or fuzzed.

    Can you view other users' data?

    Is access control properly enforced?

🔸 2. /api/swagger/v1/users

    Lists all users?

    Is pagination present or can you retrieve all users in one go?

    Can you POST to create a new user?

    Try different HTTP methods (PUT, DELETE, etc.)

🔸 3. /api/swagger/v1

    The version root.

    Commonly contains all v1 endpoints (users, posts, products, etc.).

    Check for other directories:

        /api/swagger/v1/posts

        /api/swagger/v1/products

    Also test undocumented methods here (e.g., OPTIONS to discover allowed methods).

🔸 4. /api/swagger

    This might be a namespace or a documentation route (e.g., Swagger UI).

    Check if Swagger/OpenAPI documentation is exposed:

        /api/swagger.json

        /api/swagger.yaml

        /api/swagger/ui

    Documentation files can reveal the full API structure, schemas, parameters, and auth details.

🔸 5. /api

    Top-level entry point to the API.

    Might expose multiple services or versions:

        /api/v1

        /api/v2

        /api/dev

    Might have discovery endpoints (e.g., /api/health, /api/status, /api/routes).