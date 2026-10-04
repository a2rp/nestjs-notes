# 14. Testing NestJS applications

[Back to notes index](../README.md)

| [Previous: Logging and application lifecycle](./13-logging-and-application-lifecycle.md) | [Notes index](../README.md) | [Next: Security, CORS, and API boundaries](./15-security-cors-and-api-boundaries.md) |
| --- | --- | --- |

## Test a class at the right boundary

A unit test checks one class with its direct dependencies controlled. A module test uses Nest's testing utilities to build a small dependency graph. An end-to-end test starts the application and exercises the HTTP boundary.

Use the test runner generated for the project. Current Nest CLI setups can choose different module formats and test runners, so keep the runner imports and scripts consistent with the generated `package.json`.

## Build a testing module

This example replaces `CatsService` with a small fake so the controller test does not need a database:

~~~javascript
import assert from 'node:assert/strict'
import { Test } from '@nestjs/testing'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'

const expectedCats = [{ id: 'cat-1', name: 'Miso' }]
const fakeCatsService = {
  findAll: async () => expectedCats
}

const moduleRef = await Test.createTestingModule({
  controllers: [CatsController],
  providers: [
    { provide: CatsService, useValue: fakeCatsService }
  ]
}).compile()

try {
  const controller = moduleRef.get(CatsController)
  assert.deepEqual(await controller.findAll(), expectedCats)
} finally {
  await moduleRef.close()
}
~~~

`useValue` supplies a controlled provider under the same injection token. This test checks the controller's delegation and response without opening a database connection.

## Test the HTTP boundary

An end-to-end test can create an application from a testing module, listen on an available port, send a request with `fetch`, check the status and body, then close the app in `finally`. This verifies routing, pipes, guards, serialization, and exception handling together.

Keep a database out of a test unless the behavior under test requires real database integration. If an integration test uses one, point it at a disposable test database and clean up its records.

## Test failures and edge cases

Test more than the expected successful path. Include invalid input, missing records, unauthorized access, and dependency failures where they matter. Assert stable public outcomes such as status codes and response fields rather than private implementation details.

## Check what you learned

1. What is the difference between a unit test and an end-to-end test?
2. Which Nest package provides `Test.createTestingModule()`?
3. What does the `useValue` provider do in the example?
4. Why does the controller test avoid a real database?
5. What application behavior does an HTTP-level test exercise?
6. Why should a test close its module or application?
7. Name two failure cases worth testing.
8. Why assert public response behavior instead of private implementation details?

## References

- [Testing](https://docs.nestjs.com/fundamentals/testing)
- [Nest testing module](https://docs.nestjs.com/fundamentals/testing#testing-utilities)
- [Node.js test runner](https://nodejs.org/api/test.html)
