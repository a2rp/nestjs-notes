# 10. Interceptors and response handling

[Back to notes index](../README.md)

| [Previous: Guards, authentication, and authorization](./09-guards-authentication-and-authorization.md) | [Notes index](../README.md) | [Next: Configuration and environment values](./11-configuration-and-environment.md) |
| --- | --- | --- |

## Where an interceptor fits

An interceptor can run code before a controller handler and process the handler's result on the way back. It receives an execution context and a handler stream. Interceptors are useful for cross-cutting work such as response mapping, timing, and caching.

A JavaScript interceptor implements an `intercept()` method and returns the next handler's observable:

~~~javascript
import { Injectable } from '@nestjs/common'
import { map } from 'rxjs/operators'

@Injectable()
export class WrapResponseInterceptor {
  intercept(context, next) {
    return next.handle().pipe(
      map((data) => ({ data }))
    )
  }
}
~~~

The `map()` operator changes the returned value while preserving the handler's asynchronous stream.

## Apply an interceptor

Register an interceptor on one route, a controller, or globally depending on where the behavior belongs:

~~~javascript
import { Controller, Get, UseInterceptors } from '@nestjs/common'
import { WrapResponseInterceptor } from './wrap-response.interceptor.js'

@Controller('status')
export class StatusController {
  @Get()
  @UseInterceptors(WrapResponseInterceptor)
  getStatus() {
    return { ready: true }
  }
}
~~~

The client receives an object shaped like `{ data: { ready: true } }`. Changing every response shape affects API consumers, so use a wrapper only when it is part of the documented response contract.

## Measure a request without exposing its data

An interceptor can measure time and record a duration without logging a request body or returned value:

~~~javascript
import { Injectable } from '@nestjs/common'
import { finalize } from 'rxjs/operators'

@Injectable()
export class TimingInterceptor {
  intercept(context, next) {
    const startedAt = Date.now()

    return next.handle().pipe(
      finalize(() => {
        const durationMs = Date.now() - startedAt
        console.log(`Request completed in ${durationMs} ms`)
      })
    )
  }
}
~~~

In a real service, send this measurement to the configured logger or monitoring system. Avoid logging secrets or private response content.

## Interceptors and exceptions

An interceptor can observe the stream before and after the handler. If a handler throws, Nest's exception layer creates the error response. Use exception filters for centralized error formatting and interceptors for cross-cutting request or result behavior.

## Check what you learned

1. When can an interceptor run relative to a route handler?
2. What does `next.handle()` return to the interceptor?
3. Which RxJS operator changes a successful result value?
4. Where can an interceptor be applied?
5. Why should a response wrapper be part of a documented API contract?
6. What does `finalize()` allow a timing interceptor to do?
7. Which layer is better suited to centralized HTTP error formatting?
8. What should not be written to request timing logs?

## References

- [Interceptors](https://docs.nestjs.com/interceptors)
- [Execution context](https://docs.nestjs.com/fundamentals/execution-context)
- [Exception filters](https://docs.nestjs.com/exception-filters)
