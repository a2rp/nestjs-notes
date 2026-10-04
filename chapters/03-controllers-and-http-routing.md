# 3. Controllers and HTTP routing

[Back to notes index](../README.md)

| [Previous: Modules and application structure](./02-modules-and-application-structure.md) | [Notes index](../README.md) | [Next: Providers and dependency injection](./04-providers-and-dependency-injection.md) |
| --- | --- | --- |

## Map a URL to a controller

A controller handles requests for one area of an API. `@Controller('cats')` adds a route prefix. Method decorators such as `@Get()` and `@Post()` select the HTTP method and optional path:

~~~javascript
import { Controller, Get, Param, Bind } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get()
  findAll() {
    return [{ id: 'cat-1', name: 'Miso' }]
  }

  @Get('summary')
  getSummary() {
    return { total: 1 }
  }

  @Get(':id')
  @Bind(Param('id'))
  findOne(id) {
    return { id, name: 'Miso' }
  }
}
~~~

The route prefix and method path combine. These handlers map to `GET /cats`, `GET /cats/summary`, and `GET /cats/:id`.

## Use JavaScript request bindings

JavaScript does not use TypeScript-style parameter annotations. Nest provides `@Bind()` so method parameter decorators can be attached to a route handler in JavaScript:

~~~javascript
import { Bind, Body, Controller, Post } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Body())
  create(createCat) {
    return { id: 'cat-2', ...createCat }
  }
}
~~~

This example reads a request body but does not validate it yet. Input validation is covered in a later chapter. Never treat a request body as trusted simply because Nest can parse it.

## Understand default responses

Nest's standard response handling serializes returned objects and arrays as JSON. A successful `GET` normally returns status `200`. A successful `POST` normally returns `201`. Use `@HttpCode()` when a route needs another fixed status:

~~~javascript
import { Controller, Get, HttpCode } from '@nestjs/common'

@Controller('health')
export class HealthController {
  @Get()
  @HttpCode(204)
  check() {
    return
  }
}
~~~

Returning a value is usually simpler than injecting the Express response object. A route that uses `@Res()` takes responsibility for sending the response unless passthrough mode is configured.

## Avoid route shadowing

Declare static paths such as `/cats/summary` before a broad parameter route such as `/cats/:id`. A parameter route can otherwise match the word `summary` first, depending on the adapter's resolution order.

Use one controller for a focused resource area. Keep database or business rules in providers so HTTP routing remains easy to inspect and test.

## Check what you learned

1. What does `@Controller('cats')` add to the route?
2. How do the controller prefix and method path combine?
3. What does `@Get(':id')` match?
4. Why does JavaScript Nest code use `@Bind()` with `@Param()` or `@Body()`?
5. What does Nest normally serialize when a handler returns an object?
6. What status code does a successful `POST` use by default?
7. Why should static routes be declared before broad parameter routes?
8. Why keep business rules out of the controller?

## References

- [Controllers](https://docs.nestjs.com/controllers)
- [Request payloads](https://docs.nestjs.com/controllers#request-payloads)
- [Route parameters](https://docs.nestjs.com/controllers#route-parameters)
