---
name: test-api
when_to_use: When the user wants to test, unit-test, or integration-test a CRUD API or its generated endpoints.
---

If the user wants to test a CRUD API, suggest executing API endpoints in the following order:

1. Create new entity
2. Read entities to verify the above entity was correctly inserted
3. Update the entity created above
4. Read the entity to verify it was correctly updated
5. Delete the entity created above
6. Read entities to verify the entity created above was actually deleted

Follow the above recipe for all CRUD endpoints generated. Just create and use some example dummy data for columns. The point is to *"unit test"* the API, or to perform an integration test.

This order lets you verify CRUD APIs are working as expected. If the generated API requires a JWT token, you can ask the user to generate one for you using the *"[username]/Generate Token"* menu command and give it to you.
