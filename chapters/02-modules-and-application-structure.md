# 2. Modules and application structure

[Back to notes index](../README.md)

| [Previous: NestJS and a JavaScript application](./01-nestjs-and-javascript-application.md) | [Notes index](../README.md) | [Next: Controllers and HTTP routing](./03-controllers-and-http-routing.md) |
| --- | --- | --- |

## Group a feature in a module

A Nest module groups the controllers and providers that belong to an application area. A feature module keeps related files together and makes its public dependencies explicit:

~~~text
src/
├── app.module.js
└── cats/
    ├── cats.controller.js
    ├── cats.module.js
    └── cats.service.js
~~~

Create a module with the CLI from the project root:

~~~sh
nest generate module cats
~~~

The generator uses the project language setting. Check that the generated file uses `.js` and the project's established module syntax.

## Register a controller and provider

The `@Module()` decorator accepts metadata that describes the feature:

~~~javascript
import { Module } from '@nestjs/common'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'

@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService]
})
export class CatsModule {}
~~~

- `controllers` lists request handlers owned by the module.
- `providers` lists injectable classes or provider definitions.
- `imports` lists other modules whose exported providers are needed here.
- `exports` exposes selected providers to modules that import this module.

A provider is private to its module unless it is exported and made available through an imported module.

## Add the feature to the root module

The root module imports the feature module so Nest can build the complete application graph:

~~~javascript
import { Module } from '@nestjs/common'
import { CatsModule } from './cats/cats.module.js'

@Module({
  imports: [CatsModule]
})
export class AppModule {}
~~~

Do not register the same service separately in every module that needs it if those modules should share one instance. Export it from its owning module and import that module where required.

## Keep module boundaries useful

A module should represent a feature or shared capability with a clear purpose. Keep controllers close to the providers they call. Export only what another module needs. This makes dependencies easier to locate and reduces accidental coupling.

Global modules can make providers available without repeated imports, but they hide where a dependency comes from. Prefer explicit imports unless a truly cross-cutting provider needs to be global.

## Check what you learned

1. What does a Nest module group together?
2. Which module property registers request handlers?
3. Which property registers injectable application classes?
4. How does a provider become available to another module?
5. What does the root module do?
6. Why should a shared service be exported from its owning module?
7. What can make a global module harder to maintain?
8. Where should a feature's controller and provider files usually live?

## References

- [Modules](https://docs.nestjs.com/modules)
- [Nest CLI generators](https://docs.nestjs.com/cli/usages)
- [Providers](https://docs.nestjs.com/providers)
