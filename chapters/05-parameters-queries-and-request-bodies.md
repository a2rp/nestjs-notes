# 5. Route parameters, query values, and request bodies

[Back to notes index](../README.md)

| [Previous: Providers and dependency injection](./04-providers-and-dependency-injection.md) | [Notes index](../README.md) | [Next: Validation and transformation with pipes](./06-validation-and-pipes.md) |
| --- | --- | --- |

## Read a route parameter

A route parameter is part of the URL path, such as the identifier in `/cats/cat-7`. JavaScript handlers bind it with `@Bind(Param(...))`:

~~~javascript
import { Bind, Controller, Get, Param } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get(':id')
  @Bind(Param('id'))
  findOne(id) {
    return { id }
  }
}
~~~

The `id` arrives as text. If the application expects a number or UUID, validate and transform it before using it, covered in the next chapter.

## Read query string values

Query values follow `?` in the URL. For `/cats?active=true&page=2`, the handler can bind both values:

~~~javascript
import { Bind, Controller, Get, Query } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get()
  @Bind(Query('active'), Query('page'))
  findAll(active, page) {
    return { active, page }
  }
}
~~~

Query string values also arrive as strings. A value such as `page=2` needs validation before it is treated as a number. Missing values should have a deliberate default or produce a clear client error.

## Read a JSON request body

A `POST` or `PATCH` request can send JSON in its body. Bind the whole body when the route needs a small request object:

~~~javascript
import { Bind, Body, Controller, Post } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Body())
  create(input) {
    return {
      id: 'cat-9',
      name: input.name
    }
  }
}
~~~

Alternatively, bind a specific body field with `Body('name')`. Reading the input does not prove that the field exists or has the right type. Validate the request before passing it to a provider.

## Request metadata and response values

Nest also provides bindings for the request object, headers, cookies, and client address. Prefer the focused decorators such as `@Param()`, `@Query()`, and `@Body()` when they are enough. Using the underlying request object directly couples a handler more closely to the HTTP adapter.

Return plain objects or arrays for JSON responses. Keep the response shape intentional: expose only fields clients need, and do not return internal persistence records that contain private data.

## Check what you learned

1. Where does a route parameter appear?
2. How does JavaScript bind an `id` route parameter?
3. What type do route and query values arrive as by default?
4. Where do query string values appear in a URL?
5. Which decorator reads a JSON request body?
6. Does reading a request body validate its fields?
7. Why prefer focused request decorators over the full request object?
8. What should be considered before returning an object to a client?

## References

- [Route parameters](https://docs.nestjs.com/controllers#route-parameters)
- [Query parameters](https://docs.nestjs.com/controllers#query-parameters)
- [Request payloads](https://docs.nestjs.com/controllers#request-payloads)
- [Request object](https://docs.nestjs.com/controllers#request-object)
