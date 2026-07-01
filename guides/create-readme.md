---
name: create-readme
when_to_use: When the user asks you to generate a README file for a module.
---

# Create a README file for a module

When the user asks you to generate a README for a module, list all files in the module, read the content of each, retrieve the module's OpenAPI specification, and use that information to create a `README.md` saved into the module's folder.

**Process**

1. List all files in the module folder (`list-files`).
2. Read the content of each file (`read-file`).
3. Retrieve the OpenAPI specification for the module (`get-openapi-spec`).
4. Create a `README.md` and display it to the user.
5. If the user is satisfied, save it as `README.md` in the module's folder (`create-file`).
