# 12. Persistence and data access patterns

[Back to notes index](../README.md)

| [Previous: Configuration and environment values](./11-configuration-and-environment.md) | [Notes index](../README.md) | [Next: Logging and application lifecycle](./13-logging-and-application-lifecycle.md) |
| --- | --- | --- |

## Keep persistence behind a provider

A controller should not own database connection details. Keep persistence calls in a provider or repository so request handling, validation, and storage responsibilities remain clear. Nest has integrations for several database libraries. This chapter uses Mongoose with MongoDB as one concrete JavaScript example.

Install the Nest integration and Mongoose package:

~~~sh
npm install @nestjs/mongoose mongoose
~~~

Read the connection string from configuration rather than writing a password into a source file:

~~~javascript
import { Module } from '@nestjs/common'
import { MongooseModule } from '@nestjs/mongoose'
import { CatsModule } from './cats/cats.module.js'

@Module({
  imports: [
    MongooseModule.forRoot(process.env.MONGODB_URI),
    CatsModule
  ]
})
export class AppModule {}
~~~

Validate that the environment value exists before connecting. Production deployments should use protected secret settings and a verified TLS connection.

## Define a schema with JavaScript

A Mongoose schema describes stored fields and Mongoose-level validation. In JavaScript, define it directly instead of depending on reflected property types:

~~~javascript
import mongoose from 'mongoose'

export const CatSchema = new mongoose.Schema({
  name: { type: String, required: true, trim: true },
  age: { type: Number, min: 0 },
  breed: { type: String, trim: true }
}, {
  timestamps: true
})
~~~

Register the schema with the feature module:

~~~javascript
import { Module } from '@nestjs/common'
import { MongooseModule } from '@nestjs/mongoose'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'
import { CatSchema } from './cat.schema.js'

@Module({
  imports: [MongooseModule.forFeature([{ name: 'Cat', schema: CatSchema }])],
  controllers: [CatsController],
  providers: [CatsService]
})
export class CatsModule {}
~~~

## Inject and use the model

`getModelToken()` returns the provider token for a registered model. `@Dependencies()` makes that constructor dependency explicit in JavaScript:

~~~javascript
import { Dependencies, Injectable } from '@nestjs/common'
import { getModelToken } from '@nestjs/mongoose'

const CAT_MODEL = getModelToken('Cat')

@Injectable()
@Dependencies(CAT_MODEL)
export class CatsService {
  constructor(catModel) {
    this.catModel = catModel
  }

  async create(input) {
    const cat = new this.catModel(input)
    return cat.save()
  }

  async findAll() {
    return this.catModel.find().select('name age breed').lean().exec()
  }
}
~~~

A lean query returns plain objects rather than full Mongoose documents. Select needed fields and constrain list reads with filters and limits in an API. Handle database errors at the provider or application boundary and translate expected conflicts into useful HTTP responses.

## Keep validation and persistence rules distinct

A Mongoose schema validates writes made through Mongoose. It does not automatically protect writes made by every other tool or service. Validate request data at the API boundary and add database-level validation or indexes where collection-wide rules must also hold outside the application.

Avoid passing arbitrary request objects directly to a model update. Pick allowed fields explicitly so a client cannot change private properties by adding unexpected keys.

## Check what you learned

1. Why keep database calls out of a controller?
2. Which packages connect a Nest module to Mongoose?
3. Where should a production connection string come from?
4. What does a Mongoose schema describe?
5. How is a JavaScript model registered in a feature module?
6. How does `getModelToken()` help inject a model?
7. What does `.lean()` return?
8. Why should arbitrary request objects not be passed directly into an update?

## References

- [NestJS MongoDB integration](https://docs.nestjs.com/techniques/mongodb)
- [Mongoose schemas](https://mongoosejs.com/docs/guide.html)
- [Mongoose model queries](https://mongoosejs.com/docs/queries.html)
- [NestJS database integrations](https://docs.nestjs.com/techniques/database)
