# 8. Middleware and request lifecycle

[Back to notes index](../README.md)

| [Previous: Exceptions and HTTP error responses](./07-exceptions-and-http-errors.md) | [Notes index](../README.md) | [Next: Guards, authentication, and authorization](./09-guards-authentication-and-authorization.md) |
| --- | --- | --- |

## What middleware is for

Middleware runs before a route handler. It can inspect or change the request and response objects, add request context, or decide whether to pass control onward. Typical uses include request identifiers and small transport-level tasks.

A class middleware in JavaScript provides a `use()` method. It must call `next()` to allow the request to continue, or end the response itself:

~~~javascript
import { randomUUID } from 'node:crypto'

export class RequestIdMiddleware {
  use(request, response, next) {
    request.requestId = randomUUID()
    response.setHeader('X-Request-Id', request.requestId)
    next()
  }
}
~~~

Avoid logging authorization headers, cookies, passwords, or full request bodies. A request identifier should help correlate logs without copying sensitive input into them.

## Apply middleware to routes

A module can attach middleware to selected routes using `configure()`:

~~~javascript
import { Module } from '@nestjs/common'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'
import { RequestIdMiddleware } from './request-id.middleware.js'

@Module({
  controllers: [CatsController],
  providers: [CatsService]
})
export class CatsModule {
  configure(consumer) {
    consumer.apply(RequestIdMiddleware).forRoutes('cats')
  }
}
~~~

Use route matching to scope middleware where possible. Middleware can also be registered globally at application bootstrap when every request needs the same behavior.

## Follow a request through Nest

A simplified HTTP request proceeds through these stages:

1. Middleware can inspect the raw request and continue with `next()`.
2. Guards decide whether the request may reach the handler.
3. Interceptors can run logic before the handler.
4. Pipes validate or transform bound inputs.
5. The controller calls providers and returns a result.
6. Interceptors can process the result on its way back.
7. Exception filters handle uncaught errors.

Knowing this order helps place behavior at the right boundary. Authentication policy belongs in guards, input transformation in pipes, and cross-cutting response work in interceptors.

## Choose the right layer

Keep middleware small and independent of feature business rules. A guard can use Nest dependency injection and route metadata, while raw middleware is closer to the underlying HTTP platform. Use middleware when the task genuinely belongs before Nest's route execution.

## Check what you learned

1. At what point does middleware run?
2. What must middleware call to continue processing a request?
3. Name one reasonable use for middleware.
4. Why should request identifiers avoid copying user credentials?
5. How can a module apply middleware to selected routes?
6. Which stage can decide whether a caller may reach a handler?
7. Which stages validate inputs and handle uncaught exceptions?
8. Why keep middleware independent of feature business rules?

## References

- [Middleware](https://docs.nestjs.com/middleware)
- [Request lifecycle](https://docs.nestjs.com/faq/request-lifecycle)
- [Guards](https://docs.nestjs.com/guards)
- [Interceptors](https://docs.nestjs.com/interceptors)
