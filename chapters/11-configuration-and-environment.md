# 11. Configuration and environment values

[Back to notes index](../README.md)

| [Previous: Interceptors and response handling](./10-interceptors-and-response-handling.md) | [Notes index](../README.md) | [Next: Persistence and data access patterns](./12-persistence-and-data-access.md) |
| --- | --- | --- |

## Keep environment-specific values outside source

A service often needs a port, database URL, or external service key. These values differ between local development, testing, and deployment. Keep them outside source files so the same code can run in each environment.

Nest's configuration package loads environment values and makes them available through a provider. Add the package to a project that needs it:

~~~sh
npm install @nestjs/config
~~~

## Register configuration once

`ConfigModule.forRoot()` loads the project's environment settings. `isGlobal: true` makes the configuration provider available across modules without repeated imports:

~~~javascript
import { Module } from '@nestjs/common'
import { ConfigModule } from '@nestjs/config'
import { CatsModule } from './cats/cats.module.js'

@Module({
  imports: [
    ConfigModule.forRoot({ isGlobal: true }),
    CatsModule
  ]
})
export class AppModule {}
~~~

If the configuration module is not global, import it into each module that needs its exported providers.

## Inject and validate a value

Environment variables are strings. Convert and validate them before using them as a number:

~~~javascript
import { Dependencies, Injectable } from '@nestjs/common'
import { ConfigService } from '@nestjs/config'

@Injectable()
@Dependencies(ConfigService)
export class ServerSettings {
  constructor(configService) {
    const rawPort = configService.get('PORT', '3000')
    const port = Number(rawPort)

    if (!Number.isInteger(port) || port < 1 || port > 65535) {
      throw new Error('PORT must be a valid TCP port')
    }

    this.port = port
  }
}
~~~

Register `ServerSettings` as a provider in the module that uses it. Fail during startup when a required value is missing or invalid instead of waiting for a request to reveal the configuration problem.

## Protect secrets

A `.env.example` file can document the names of required settings using safe sample values. Keep real `.env` files out of Git, and use the deployment platform's protected secret settings in hosted environments. Never log a database URI or secret token.

Configuration can also come from a secret manager, container environment, or cloud platform. Choose one source of truth per deployment and validate the final values at startup.

## Check what you learned

1. Why keep environment-specific values outside source files?
2. Which package provides `ConfigModule` and `ConfigService`?
3. What does `isGlobal: true` change?
4. What type does an environment variable have when first read?
5. Why validate a port before starting the server?
6. What belongs in `.env.example`?
7. Where should production secrets be stored?
8. Why validate required configuration during startup?

## References

- [Configuration](https://docs.nestjs.com/techniques/configuration)
- [NestJS Config package](https://www.npmjs.com/package/@nestjs/config)
- [Environment variables](https://nodejs.org/api/process.html#processenv)
