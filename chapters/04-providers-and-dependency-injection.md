# 4. Providers and dependency injection

[Back to notes index](../README.md)

| [Previous: Controllers and HTTP routing](./03-controllers-and-http-routing.md) | [Notes index](../README.md) | [Next: Route parameters, query values, and request bodies](./05-parameters-queries-and-request-bodies.md) |
| --- | --- | --- |

## Put application logic in a provider

A provider is a class or value that Nest can create and supply to other classes. Services are common providers. A controller can delegate work to a service instead of owning data and business rules itself.

~~~javascript
import { Injectable } from '@nestjs/common'

@Injectable()
export class CatsService {
  constructor() {
    this.cats = []
  }

  create(cat) {
    this.cats.push(cat)
    return cat
  }

  findAll() {
    return this.cats
  }
}
~~~

This in-memory array is only for learning. It is reset when the process restarts and is not durable persistence.

## Inject the service into a controller

In JavaScript, declare constructor dependencies with Nest's `@Dependencies()` decorator. Nest then constructs the controller and passes it an instance of the registered provider:

~~~javascript
import { Bind, Body, Controller, Dependencies, Get, Post } from '@nestjs/common'
import { CatsService } from './cats.service.js'

@Controller('cats')
@Dependencies(CatsService)
export class CatsController {
  constructor(catsService) {
    this.catsService = catsService
  }

  @Post()
  @Bind(Body())
  create(cat) {
    return this.catsService.create(cat)
  }

  @Get()
  findAll() {
    return this.catsService.findAll()
  }
}
~~~

Explicit injection matters in JavaScript because constructor parameter types are not emitted as runtime metadata. `@Dependencies(CatsService)` tells Nest which provider token to pass to the constructor.

## Register providers in a module

Nest can inject only a provider that is registered in the module scope or exported from an imported module:

~~~javascript
import { Module } from '@nestjs/common'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'

@Module({
  controllers: [CatsController],
  providers: [CatsService]
})
export class CatsModule {}
~~~

If another module needs the service, add it to `exports` and import `CatsModule` from the consuming module. This makes the dependency visible in the module graph.

## Use a token for a non-class dependency

Configuration objects and other values can be registered under a token. The consumer injects that token explicitly:

~~~javascript
export const APP_LABEL = 'APP_LABEL'
~~~

~~~javascript
import { Dependencies, Injectable } from '@nestjs/common'
import { APP_LABEL } from './app.constants.js'

@Injectable()
@Dependencies(APP_LABEL)
export class GreetingService {
  constructor(appLabel) {
    this.appLabel = appLabel
  }

  getLabel() {
    return this.appLabel
  }
}
~~~

Register the value in a module with a custom provider object:

~~~javascript
providers: [
  GreetingService,
  { provide: APP_LABEL, useValue: 'Study API' }
]
~~~

For a string token, the JavaScript constructor can also use `@Inject(APP_LABEL)` on a property or an explicit factory provider. Prefer class tokens for ordinary service dependencies and use custom tokens for values or interchangeable implementations.

## Understand provider lifetime

Providers are singleton-scoped by default. Nest creates them for the application lifecycle and shares the instance with consumers in its module graph. Request-scoped providers are available for specific cases such as request-bound state, but they change how dependencies are created and should be used only when needed.

## Check what you learned

1. What is a provider in NestJS?
2. Why is a service a useful place for application logic?
3. What does `@Injectable()` mark?
4. How does a JavaScript controller declare constructor dependencies?
5. Why does JavaScript need explicit `@Dependencies()` metadata?
6. Where must a provider be registered before Nest can inject it?
7. When might a custom provider token be useful?
8. What is the default provider lifetime?

## References

- [Providers](https://docs.nestjs.com/providers)
- [Modules and provider scope](https://docs.nestjs.com/modules)
- [Custom providers](https://docs.nestjs.com/fundamentals/custom-providers)

