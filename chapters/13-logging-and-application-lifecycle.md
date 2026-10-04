# 13. Logging and application lifecycle

[Back to notes index](../README.md)

| [Previous: Persistence and data access patterns](./12-persistence-and-data-access.md) | [Notes index](../README.md) | [Next: Testing NestJS applications](./14-testing-nestjs-applications.md) |
| --- | --- | --- |

## Use the Nest logger

Nest's `Logger` provides structured levels and a context label. Create a logger for a class and record useful events without printing credentials or entire request bodies:

~~~javascript
import { Injectable, Logger } from '@nestjs/common'

@Injectable()
export class CatsService {
  constructor() {
    this.logger = new Logger(CatsService.name)
  }

  async findAll() {
    this.logger.debug('Loading cats')
    return []
  }
}
~~~

Use `log`, `error`, `warn`, `debug`, or `verbose` based on the event. In production, configure log levels and connect the application logger to the platform's log collection system.

## React to module lifecycle events

Providers can define lifecycle methods that Nest calls during application startup and shutdown. JavaScript classes can use the method names directly:

~~~javascript
import { Injectable, Logger } from '@nestjs/common'

@Injectable()
export class DatabaseStatusService {
  constructor() {
    this.logger = new Logger(DatabaseStatusService.name)
  }

  async onModuleInit() {
    this.logger.log('Database status provider initialized')
  }

  async onApplicationShutdown(signal) {
    this.logger.log(`Application shutdown started: ${signal || 'requested'}`)
  }
}
~~~

Use startup hooks for initialization that depends on injected providers. Use shutdown hooks to close resources your code explicitly owns. Database integration packages generally manage their own connection lifecycle.

## Enable operating system shutdown hooks

For Nest to respond to process termination signals, enable shutdown hooks during bootstrap:

~~~javascript
async function bootstrap() {
  const app = await NestFactory.create(AppModule)
  app.enableShutdownHooks()
  await app.listen(process.env.PORT ?? 3000)
}
~~~

Graceful shutdown gives the application a chance to stop accepting work and release resources. It does not replace deployment health checks or a bounded shutdown timeout.

## Keep logs useful and safe

Include stable identifiers and operation context when appropriate. Avoid passwords, authorization headers, cookies, full payment details, and sensitive request bodies. Use consistent fields so operators can search by request identifier, route, and outcome.

## Check what you learned

1. What does a logger context label help identify?
2. Name two Nest logger levels.
3. What is one safe event to log from a service?
4. What is a provider lifecycle hook?
5. When might `onModuleInit()` run?
6. What does `enableShutdownHooks()` allow the application to handle?
7. Why is graceful shutdown useful?
8. Which values should not be written into application logs?

## References

- [Logging](https://docs.nestjs.com/techniques/logger)
- [Lifecycle events](https://docs.nestjs.com/fundamentals/lifecycle-events)
- [Application shutdown hooks](https://docs.nestjs.com/fundamentals/lifecycle-events#application-shutdown)
