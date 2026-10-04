# 1. NestJS and a JavaScript application

[Back to notes index](../README.md)

| [Previous: Notes index](../README.md) | [Notes index](../README.md) | [Next: Modules and application structure](./02-modules-and-application-structure.md) |
| --- | --- | --- |

## What NestJS adds to Node.js

NestJS is a framework for building server-side applications on Node.js. It organizes an application around modules, controllers, and providers. A controller maps an incoming request to a handler, while providers hold reusable application logic and can be injected where they are needed.

Nest supports plain JavaScript. It uses modern language features such as decorators, so a JavaScript project needs Babel configuration to run the syntax Nest relies on. The Nest CLI can generate a JavaScript project and its supporting setup.

## Create a JavaScript project

Install or run the Nest CLI according to the current setup guide, then request JavaScript explicitly:

~~~sh
nest new study-api --language js
~~~

The CLI asks setup questions that can vary by version, including module format. Follow the generated project's choice rather than mixing CommonJS and ECMAScript module syntax. Inspect its `package.json`, Babel configuration, and start scripts before changing compiler settings.

Start the generated app in development mode using its package script:

~~~sh
cd study-api
npm run start:dev
~~~

The development script should start the application and watch files for changes. Check the CLI output for the port, then request the root route, commonly `http://localhost:3000/`.

## Know the entry files

A JavaScript project commonly has an entry point and a root module under `src/`:

~~~text
src/
├── app.controller.js
├── app.module.js
├── app.service.js
└── main.js
~~~

The exact files depend on the generated project options. `main.js` bootstraps Nest. The root module registers application components. A controller receives HTTP requests, and a service can hold logic used by that controller.

## Bootstrap the application

The entry point asks `NestFactory` to create an application from the root module, then begins listening for requests:

~~~javascript
import { NestFactory } from '@nestjs/core'
import { AppModule } from './app.module.js'

async function bootstrap() {
  const app = await NestFactory.create(AppModule)
  const port = process.env.PORT ?? 3000
  await app.listen(port)
}

bootstrap().catch((error) => {
  console.error('Application startup failed', error)
  process.exitCode = 1
})
~~~

Use the import format and file extensions expected by the generated module system. The command-line project handles the required compilation step for the chosen setup. Avoid copying a Babel configuration from a different Nest generation without checking the current generated files.

## Understand the request flow

A browser or client sends an HTTP request to the listening application. Nest matches its method and path to a controller handler. The handler can call a provider and return a value. Nest turns the returned object into an HTTP response using the configured platform adapter.

## Check what you learned

1. What does NestJS add to a Node.js application?
2. Which CLI option asks for JavaScript project files?
3. Why does a plain JavaScript Nest project need Babel setup?
4. What is the role of `main.js`?
5. What does the root module register?
6. Which class bootstraps a Nest application?
7. Why should module syntax match the generated project configuration?
8. Trace a request from the client to the returned response.

## References

- [NestJS first steps and JavaScript support](https://docs.nestjs.com/first-steps)
- [Nest CLI](https://docs.nestjs.com/cli/overview)
- [NestJS controllers](https://docs.nestjs.com/controllers)
- [Babel documentation](https://babeljs.io/docs/)
