# 9. Guards, authentication, and authorization

[Back to notes index](../README.md)

| [Previous: Middleware and request lifecycle](./08-middleware-and-request-lifecycle.md) | [Notes index](../README.md) | [Next: Interceptors and response handling](./10-interceptors-and-response-handling.md) |
| --- | --- | --- |

## Separate identity from permission

Authentication answers who is making a request. Authorization answers whether that authenticated identity may perform a particular action. A secure application verifies credentials before trusting a user object or role.

A guard runs before a route handler and can allow or reject the request. Nest commonly uses guards for authentication and authorization because guards can access route metadata and the execution context.

## Describe a route's required role

A small custom decorator can attach role metadata to a route:

~~~javascript
import { SetMetadata } from '@nestjs/common'

export const Roles = (...roles) => SetMetadata('roles', roles)
~~~

The roles come from trusted server-side policy. Do not accept a role name from a request body and treat it as proof of permission.

## Check role metadata in a JavaScript guard

The authentication layer must verify the credential and set `request.user`. This guard reads the required role and rejects missing identity or insufficient permission:

~~~javascript
import {
  Dependencies,
  ForbiddenException,
  Injectable,
  UnauthorizedException
} from '@nestjs/common'
import { Reflector } from '@nestjs/core'

@Injectable()
@Dependencies(Reflector)
export class RolesGuard {
  constructor(reflector) {
    this.reflector = reflector
  }

  canActivate(context) {
    const requiredRoles = this.reflector.get('roles', context.getHandler()) || []
    const request = context.switchToHttp().getRequest()

    if (!request.user) {
      throw new UnauthorizedException()
    }

    if (requiredRoles.length > 0 && !requiredRoles.includes(request.user.role)) {
      throw new ForbiddenException()
    }

    return true
  }
}
~~~

In JavaScript, constructor injection is declared with `@Dependencies(Reflector)`. Register `RolesGuard` in the module's `providers` before applying it as a class so Nest can resolve its dependency.

## Protect a route

~~~javascript
import { Controller, Get, UseGuards } from '@nestjs/common'
import { Roles } from './roles.decorator.js'
import { RolesGuard } from './roles.guard.js'

@Controller('reports')
@UseGuards(RolesGuard)
export class ReportsController {
  @Get('private')
  @Roles('admin')
  getPrivateReport() {
    return { report: 'restricted data' }
  }
}
~~~

This guard checks only authorization. A separate authentication guard or verified middleware must populate `request.user`. If no authentication layer has done that, the route must reject the request rather than infer identity from client-provided fields.

## Check what you learned

1. What question does authentication answer?
2. What question does authorization answer?
3. When does a guard run in the request lifecycle?
4. Where does the guard get its role requirement?
5. Who should populate `request.user`?
6. Why should a role not be trusted from the request body?
7. Which status is appropriate when the caller has no authenticated identity?
8. Which status is appropriate when an authenticated caller lacks permission?

## References

- [Guards](https://docs.nestjs.com/guards)
- [Authentication](https://docs.nestjs.com/security/authentication)
- [Authorization and roles](https://docs.nestjs.com/security/authorization)
- [Custom decorators](https://docs.nestjs.com/custom-decorators)
