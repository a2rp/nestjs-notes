# 15. Security, CORS, and API boundaries

[Back to notes index](../README.md)

| [Previous: Testing NestJS applications](./14-testing-nestjs-applications.md) | [Notes index](../README.md) | [Next: Production readiness and deployment](./16-production-readiness-and-deployment.md) |
| --- | --- | --- |

## Set security defaults at the application edge

Security starts with the application boundary. Validate input, authenticate callers, check permissions, limit request sizes, and return only fields the caller is allowed to see. Keep database credentials and signing keys in protected environment configuration.

Nest commonly uses the Express platform by default. With that adapter, Helmet can set useful HTTP security headers:

~~~sh
npm install helmet
~~~

~~~javascript
import helmet from 'helmet'
import { NestFactory } from '@nestjs/core'
import { AppModule } from './app.module.js'

async function bootstrap() {
  const app = await NestFactory.create(AppModule)
  app.use(helmet())
  app.setGlobalPrefix('api')
  await app.listen(process.env.PORT ?? 3000)
}

bootstrap()
~~~

If the app uses Fastify, follow the matching Fastify security plugin instructions instead of passing Express middleware to that adapter.

## Configure CORS for known clients

Cross-Origin Resource Sharing controls which browser origins may read responses from an API. It does not authenticate a caller or replace authorization:

~~~javascript
app.enableCors({
  origin: ['https://app.example.com'],
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  credentials: true
})
~~~

When credentials are enabled, use explicit trusted origins rather than a wildcard. Allow only the methods and headers the browser application needs. Non-browser clients are not constrained by browser CORS enforcement.

## Protect request and response data

- Use HTTPS in production and configure proxy trust only for known reverse proxies.
- Set a request body size limit appropriate to the API.
- Validate every incoming value and reject unexpected fields.
- Use guards for authentication and authorization.
- Do not return secrets, internal database fields, or stack traces.
- Avoid logging cookies, passwords, access tokens, or private payloads.
- Apply rate limits at the application or trusted gateway for expensive or sensitive endpoints.
- Use CSRF protection when browser authentication relies on automatically sent cookies.

Security options depend on the authentication method and HTTP adapter. Review them together so one control does not create a false sense of protection.

## Keep secrets and errors controlled

Do not store `.env` files with credentials in a public repository. Rotate secrets when access changes or a value may have leaked. Return a consistent public error response and keep detailed diagnostics in protected logs.

## Check what you learned

1. Name three controls at the API boundary.
2. What does Helmet help configure?
3. What does CORS control in a browser?
4. Does CORS authenticate a caller?
5. Why should credentialed CORS use explicit origins?
6. Why can Express middleware not be assumed to work with Fastify?
7. When may CSRF protection be needed?
8. Which values should not appear in logs or public error responses?

## References

- [NestJS security](https://docs.nestjs.com/security/authentication)
- [Helmet](https://docs.nestjs.com/security/helmet)
- [CORS](https://docs.nestjs.com/security/cors)
- [Rate limiting](https://docs.nestjs.com/security/rate-limiting)
- [CSRF protection](https://docs.nestjs.com/security/csrf)
