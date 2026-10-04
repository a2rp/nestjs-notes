# 7. Exceptions and HTTP error responses

[Back to notes index](../README.md)

| [Previous: Validation and transformation with pipes](./06-validation-and-pipes.md) | [Notes index](../README.md) | [Next: Middleware and request lifecycle](./08-middleware-and-request-lifecycle.md) |
| --- | --- | --- |

## Use HTTP exceptions for expected failures

Nest includes exceptions for common HTTP outcomes. Throw the exception that matches the request problem and let Nest convert it into an HTTP response:

~~~javascript
import { Injectable, NotFoundException } from '@nestjs/common'

@Injectable()
export class CatsService {
  constructor() {
    this.cats = [{ id: 'cat-1', name: 'Miso' }]
  }

  findOne(id) {
    const cat = this.cats.find((item) => item.id === id)

    if (!cat) {
      throw new NotFoundException(`Cat ${id} was not found`)
    }

    return cat
  }
}
~~~

Other built-in exceptions include `BadRequestException`, `UnauthorizedException`, `ForbiddenException`, and `ConflictException`. Choose based on what the client can do next, not on internal implementation details.

## Let the framework format the response

An unhandled Nest HTTP exception is processed by the built-in exception layer. A `NotFoundException` normally produces a 404 response with a JSON body containing a status code, message, and error label. The exact body can be configured, so clients should rely on the status and documented API response shape rather than internal stack traces.

Do not wrap every handler in a broad `try/catch` that hides errors. Catch an error when the application can recover, translate it to a useful domain outcome, or add context safely. Otherwise let Nest's exception layer handle it.

## Add a custom exception only for a clear need

A custom exception can extend `HttpException` when a repeated application error needs a consistent status and response:

~~~javascript
import { HttpException, HttpStatus } from '@nestjs/common'

export class InventoryUnavailableException extends HttpException {
  constructor() {
    super('Requested inventory is unavailable', HttpStatus.CONFLICT)
  }
}
~~~

Keep public messages useful without disclosing SQL text, file paths, secrets, or internal infrastructure details. Log diagnostic context on the server with sensitive fields removed.

## Know when to use an exception filter

An exception filter is appropriate when an application needs centralized formatting or logging for a class of errors. It should not swallow errors silently. Start with Nest's built-in exceptions and handler; add a custom filter when a real response policy calls for it.

## Check what you learned

1. Which exception could represent a requested record that does not exist?
2. What HTTP response does a `NotFoundException` normally produce?
3. Name two other built-in HTTP exceptions.
4. Why should the exception match what the client can do next?
5. Why is a broad `try/catch` around every handler often unhelpful?
6. What is one reason to define a custom exception?
7. Which details should not appear in a public error response?
8. When might an exception filter be useful?

## References

- [Exception filters](https://docs.nestjs.com/exception-filters)
- [Built-in HTTP exceptions](https://docs.nestjs.com/exception-filters#built-in-http-exceptions)
- [Exception filters and response body](https://docs.nestjs.com/exception-filters#custom-exceptions)
