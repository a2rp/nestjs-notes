# 6. Validation and transformation with pipes

[Back to notes index](../README.md)

| [Previous: Route parameters, query values, and request bodies](./05-parameters-queries-and-request-bodies.md) | [Notes index](../README.md) | [Next: Exceptions and HTTP error responses](./07-exceptions-and-http-errors.md) |
| --- | --- | --- |

## What a pipe does

A pipe runs before a route handler. It can transform an input value or reject it with an exception. This is a useful boundary for converting route strings and checking request data before application logic uses them.

## Parse a numeric route value

Route parameters arrive as strings. `ParseIntPipe` converts a valid integer string or rejects an invalid value with a client error:

~~~javascript
import { Bind, Controller, Get, Param, ParseIntPipe } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get(':id')
  @Bind(Param('id', ParseIntPipe))
  findOne(id) {
    return { id }
  }
}
~~~

`GET /cats/12` reaches the method with the number `12`. A value such as `/cats/blue` fails before the method executes.

## Validate a request body

A JavaScript pipe can check an object without relying on generated parameter type metadata:

~~~javascript
import { BadRequestException } from '@nestjs/common'

export class CreateCatPipe {
  transform(value) {
    if (!value || typeof value !== 'object' || Array.isArray(value)) {
      throw new BadRequestException('Request body must be an object')
    }

    if (typeof value.name !== 'string' || value.name.trim().length === 0) {
      throw new BadRequestException('name must be a non-empty string')
    }

    return { name: value.name.trim() }
  }
}
~~~

Bind it to the body parameter so validation runs before `create()`:

~~~javascript
import { Bind, Body, Controller, Post } from '@nestjs/common'
import { CreateCatPipe } from './create-cat.pipe.js'

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Body(new CreateCatPipe()))
  create(input) {
    return { name: input.name }
  }
}
~~~

For a larger request contract, use a schema validation library and a pipe that applies the schema. Keep validation rules near the API boundary, return clear client errors, and avoid silently accepting fields the application does not understand.

## Transformation is not authorization

A pipe can make a value the right shape, but it does not decide whether the caller may act on it. Authentication and authorization belong in their own layers. Always validate identifiers and values even when the route is protected.

## Check what you learned

1. At what point does a pipe run?
2. What are the two common jobs of a pipe?
3. How does `ParseIntPipe` handle a route value?
4. Where is a custom body pipe attached in the JavaScript example?
5. Why check that a body is an object and not an array?
6. What should a body validator do with an invalid field?
7. Does input transformation determine whether a caller is authorized?
8. When can a schema validation library help?

## References

- [Pipes](https://docs.nestjs.com/pipes)
- [Built-in pipes](https://docs.nestjs.com/pipes#built-in-pipes)
- [Request payload validation](https://docs.nestjs.com/techniques/validation)
