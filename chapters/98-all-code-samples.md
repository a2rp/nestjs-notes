# 98. All code samples

[Back to notes index](../README.md)

| [Previous: Production readiness and deployment](./16-production-readiness-and-deployment.md) | [Notes index](../README.md) | [Next: Complete questions and answers](./99-complete-q-and-a.md) |
| --- | --- | --- |

## NestJS and a JavaScript application

Source: [Open chapter](./01-nestjs-and-javascript-application.md)

### Example 1

~~~~sh
nest new study-api --language js
~~~~

### Example 2

~~~~sh
cd study-api
npm run start:dev
~~~~

### Example 3

~~~~text
src/
├── app.controller.js
├── app.module.js
├── app.service.js
└── main.js
~~~~

### Example 4

~~~~javascript
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
~~~~

## Modules and application structure

Source: [Open chapter](./02-modules-and-application-structure.md)

### Example 1

~~~~text
src/
├── app.module.js
└── cats/
    ├── cats.controller.js
    ├── cats.module.js
    └── cats.service.js
~~~~

### Example 2

~~~~sh
nest generate module cats
~~~~

### Example 3

~~~~javascript
import { Module } from '@nestjs/common'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'

@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService]
})
export class CatsModule {}
~~~~

### Example 4

~~~~javascript
import { Module } from '@nestjs/common'
import { CatsModule } from './cats/cats.module.js'

@Module({
  imports: [CatsModule]
})
export class AppModule {}
~~~~

## Controllers and HTTP routing

Source: [Open chapter](./03-controllers-and-http-routing.md)

### Example 1

~~~~javascript
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
~~~~

### Example 2

~~~~javascript
import { Bind, Body, Controller, Post } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Body())
  create(createCat) {
    return { id: 'cat-2', ...createCat }
  }
}
~~~~

### Example 3

~~~~javascript
import { Controller, Get, HttpCode } from '@nestjs/common'

@Controller('health')
export class HealthController {
  @Get()
  @HttpCode(204)
  check() {
    return
  }
}
~~~~

## Providers and dependency injection

Source: [Open chapter](./04-providers-and-dependency-injection.md)

### Example 1

~~~~javascript
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
~~~~

### Example 2

~~~~javascript
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
~~~~

### Example 3

~~~~javascript
import { Module } from '@nestjs/common'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'

@Module({
  controllers: [CatsController],
  providers: [CatsService]
})
export class CatsModule {}
~~~~

### Example 4

~~~~javascript
export const APP_LABEL = 'APP_LABEL'
~~~~

### Example 5

~~~~javascript
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
~~~~

### Example 6

~~~~javascript
providers: [
  GreetingService,
  { provide: APP_LABEL, useValue: 'Study API' }
]
~~~~

## Route parameters, query values, and request bodies

Source: [Open chapter](./05-parameters-queries-and-request-bodies.md)

### Example 1

~~~~javascript
import { Bind, Controller, Get, Param } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get(':id')
  @Bind(Param('id'))
  findOne(id) {
    return { id }
  }
}
~~~~

### Example 2

~~~~javascript
import { Bind, Controller, Get, Query } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get()
  @Bind(Query('active'), Query('page'))
  findAll(active, page) {
    return { active, page }
  }
}
~~~~

### Example 3

~~~~javascript
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
~~~~

## Validation and transformation with pipes

Source: [Open chapter](./06-validation-and-pipes.md)

### Example 1

~~~~javascript
import { Bind, Controller, Get, Param, ParseIntPipe } from '@nestjs/common'

@Controller('cats')
export class CatsController {
  @Get(':id')
  @Bind(Param('id', ParseIntPipe))
  findOne(id) {
    return { id }
  }
}
~~~~

### Example 2

~~~~javascript
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
~~~~

### Example 3

~~~~javascript
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
~~~~

## Exceptions and HTTP error responses

Source: [Open chapter](./07-exceptions-and-http-errors.md)

### Example 1

~~~~javascript
import { Injectable, NotFoundException } from '@nestjs/common'

@Injectable()
export class CatsService {
  constructor() {
    this.cats = [{ id: 'cat-1', name: 'Miso' }]
  }

  findOne(id) {
    const cat = this.cats.find((item) => item.id === id)

    if (!cat) {
      throw new NotFoundException(`Cat ${id} was not found`)
    }

    return cat
  }
}
~~~~

### Example 2

~~~~javascript
import { HttpException, HttpStatus } from '@nestjs/common'

export class InventoryUnavailableException extends HttpException {
  constructor() {
    super('Requested inventory is unavailable', HttpStatus.CONFLICT)
  }
}
~~~~

## Middleware and request lifecycle

Source: [Open chapter](./08-middleware-and-request-lifecycle.md)

### Example 1

~~~~javascript
import { randomUUID } from 'node:crypto'

export class RequestIdMiddleware {
  use(request, response, next) {
    request.requestId = randomUUID()
    response.setHeader('X-Request-Id', request.requestId)
    next()
  }
}
~~~~

### Example 2

~~~~javascript
import { Module } from '@nestjs/common'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'
import { RequestIdMiddleware } from './request-id.middleware.js'

@Module({
  controllers: [CatsController],
  providers: [CatsService]
})
export class CatsModule {
  configure(consumer) {
    consumer.apply(RequestIdMiddleware).forRoutes('cats')
  }
}
~~~~

## Guards, authentication, and authorization

Source: [Open chapter](./09-guards-authentication-and-authorization.md)

### Example 1

~~~~javascript
import { SetMetadata } from '@nestjs/common'

export const Roles = (...roles) => SetMetadata('roles', roles)
~~~~

### Example 2

~~~~javascript
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
~~~~

### Example 3

~~~~javascript
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
~~~~

## Interceptors and response handling

Source: [Open chapter](./10-interceptors-and-response-handling.md)

### Example 1

~~~~javascript
import { Injectable } from '@nestjs/common'
import { map } from 'rxjs/operators'

@Injectable()
export class WrapResponseInterceptor {
  intercept(context, next) {
    return next.handle().pipe(
      map((data) => ({ data }))
    )
  }
}
~~~~

### Example 2

~~~~javascript
import { Controller, Get, UseInterceptors } from '@nestjs/common'
import { WrapResponseInterceptor } from './wrap-response.interceptor.js'

@Controller('status')
export class StatusController {
  @Get()
  @UseInterceptors(WrapResponseInterceptor)
  getStatus() {
    return { ready: true }
  }
}
~~~~

### Example 3

~~~~javascript
import { Injectable } from '@nestjs/common'
import { finalize } from 'rxjs/operators'

@Injectable()
export class TimingInterceptor {
  intercept(context, next) {
    const startedAt = Date.now()

    return next.handle().pipe(
      finalize(() => {
        const durationMs = Date.now() - startedAt
        console.log(`Request completed in ${durationMs} ms`)
      })
    )
  }
}
~~~~

## Configuration and environment values

Source: [Open chapter](./11-configuration-and-environment.md)

### Example 1

~~~~sh
npm install @nestjs/config
~~~~

### Example 2

~~~~javascript
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
~~~~

### Example 3

~~~~javascript
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
~~~~

## Persistence and data access patterns

Source: [Open chapter](./12-persistence-and-data-access.md)

### Example 1

~~~~sh
npm install @nestjs/mongoose mongoose
~~~~

### Example 2

~~~~javascript
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
~~~~

### Example 3

~~~~javascript
import mongoose from 'mongoose'

export const CatSchema = new mongoose.Schema({
  name: { type: String, required: true, trim: true },
  age: { type: Number, min: 0 },
  breed: { type: String, trim: true }
}, {
  timestamps: true
})
~~~~

### Example 4

~~~~javascript
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
~~~~

### Example 5

~~~~javascript
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
~~~~

## Logging and application lifecycle

Source: [Open chapter](./13-logging-and-application-lifecycle.md)

### Example 1

~~~~javascript
import { Injectable, Logger } from '@nestjs/common'

@Injectable()
export class CatsService {
  constructor() {
    this.logger = new Logger(CatsService.name)
  }

  async findAll() {
    this.logger.debug('Loading cats')
    return []
  }
}
~~~~

### Example 2

~~~~javascript
import { Injectable, Logger } from '@nestjs/common'

@Injectable()
export class DatabaseStatusService {
  constructor() {
    this.logger = new Logger(DatabaseStatusService.name)
  }

  async onModuleInit() {
    this.logger.log('Database status provider initialized')
  }

  async onApplicationShutdown(signal) {
    this.logger.log(`Application shutdown started: ${signal || 'requested'}`)
  }
}
~~~~

### Example 3

~~~~javascript
async function bootstrap() {
  const app = await NestFactory.create(AppModule)
  app.enableShutdownHooks()
  await app.listen(process.env.PORT ?? 3000)
}
~~~~

## Testing NestJS applications

Source: [Open chapter](./14-testing-nestjs-applications.md)

### Example 1

~~~~javascript
import assert from 'node:assert/strict'
import { Test } from '@nestjs/testing'
import { CatsController } from './cats.controller.js'
import { CatsService } from './cats.service.js'

const expectedCats = [{ id: 'cat-1', name: 'Miso' }]
const fakeCatsService = {
  findAll: async () => expectedCats
}

const moduleRef = await Test.createTestingModule({
  controllers: [CatsController],
  providers: [
    { provide: CatsService, useValue: fakeCatsService }
  ]
}).compile()

try {
  const controller = moduleRef.get(CatsController)
  assert.deepEqual(await controller.findAll(), expectedCats)
} finally {
  await moduleRef.close()
}
~~~~

## Security, CORS, and API boundaries

Source: [Open chapter](./15-security-cors-and-api-boundaries.md)

### Example 1

~~~~sh
npm install helmet
~~~~

### Example 2

~~~~javascript
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
~~~~

### Example 3

~~~~javascript
app.enableCors({
  origin: ['https://app.example.com'],
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  credentials: true
})
~~~~

## Production readiness and deployment

Source: [Open chapter](./16-production-readiness-and-deployment.md)

### Example 1

~~~~sh
npm ci
npm run build
npm run start:prod
~~~~

### Example 2

~~~~javascript
const port = Number(process.env.PORT ?? 3000)
await app.listen(port, '0.0.0.0')
~~~~

### Example 3

~~~~javascript
const app = await NestFactory.create(AppModule)
app.enableShutdownHooks()
await app.listen(port, '0.0.0.0')
~~~~

