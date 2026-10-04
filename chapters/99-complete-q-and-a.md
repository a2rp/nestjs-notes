# 99. Complete questions and answers

[Back to notes index](../README.md)

| [Previous: All code samples](./98-all-code-samples.md) | [Notes index](../README.md) | [Next: Notes index](../README.md) |
| --- | --- | --- |

This appendix collects the review questions from the core chapters and gives a concise answer for each one.

## 1. NestJS and a JavaScript application

1. **Question:** What does NestJS add to a Node.js application?  
   **Answer:** It provides structure through modules, controllers, providers, dependency injection, and request lifecycle tools.
2. **Question:** Which CLI option asks for JavaScript project files?  
   **Answer:** Use `--language js` when creating the project.
3. **Question:** Why does a plain JavaScript Nest project need Babel setup?  
   **Answer:** Nest relies on language features such as decorators that need the generated Babel configuration to run in JavaScript.
4. **Question:** What is the role of `main.js`?  
   **Answer:** It creates the Nest application from the root module and starts the HTTP listener.
5. **Question:** What does the root module register?  
   **Answer:** It describes the application's imported modules, controllers, and providers.
6. **Question:** Which class bootstraps a Nest application?  
   **Answer:** `NestFactory` creates the Nest application instance.
7. **Question:** Why should module syntax match the generated project configuration?  
   **Answer:** Mixing incompatible import systems can prevent the application from loading its entry files.
8. **Question:** Trace a request from the client to the returned response.  
   **Answer:** The server receives it, Nest matches it to a controller, the handler may call providers, and Nest serializes the returned value.

## 2. Modules and application structure

1. **Question:** What does a Nest module group together?  
   **Answer:** It groups controllers and providers that belong to a feature or shared capability.
2. **Question:** Which module property registers request handlers?  
   **Answer:** The `controllers` property.
3. **Question:** Which property registers injectable application classes?  
   **Answer:** The `providers` property.
4. **Question:** How does a provider become available to another module?  
   **Answer:** Its module exports the provider, and the consuming module imports that module.
5. **Question:** What does the root module do?  
   **Answer:** It imports the feature modules that make up the application graph.
6. **Question:** Why should a shared service be exported from its owning module?  
   **Answer:** Consumers reuse the owning module's provider instance and its explicit public boundary.
7. **Question:** What can make a global module harder to maintain?  
   **Answer:** Its dependencies become less visible because consumers do not show an explicit module import.
8. **Question:** Where should a feature's controller and provider files usually live?  
   **Answer:** Together in a feature directory such as `src/cats/`.

## 3. Controllers and HTTP routing

1. **Question:** What does `@Controller('cats')` add to the route?  
   **Answer:** It adds the `/cats` prefix to routes declared by that controller.
2. **Question:** How do the controller prefix and method path combine?  
   **Answer:** Nest joins them to form the full request path, such as `/cats/summary`.
3. **Question:** What does `@Get(':id')` match?  
   **Answer:** It matches a GET request with one path segment after the controller prefix.
4. **Question:** Why does JavaScript Nest code use `@Bind()` with `@Param()` or `@Body()`?  
   **Answer:** It attaches request value bindings to handler arguments in JavaScript syntax.
5. **Question:** What does Nest normally serialize when a handler returns an object?  
   **Answer:** It sends the object as a JSON response.
6. **Question:** What status code does a successful `POST` use by default?  
   **Answer:** It normally uses HTTP status `201`.
7. **Question:** Why should static routes be declared before broad parameter routes?  
   **Answer:** A parameter route can match a static path first and send it to the wrong handler.
8. **Question:** Why keep business rules out of the controller?  
   **Answer:** Providers can own reusable logic and be tested without coupling it to HTTP handling.

## 4. Providers and dependency injection

1. **Question:** What is a provider in NestJS?  
   **Answer:** It is a class or value that Nest can create and inject into other classes.
2. **Question:** Why is a service a useful place for application logic?  
   **Answer:** It keeps reusable operations separate from route parsing and response handling.
3. **Question:** What does `@Injectable()` mark?  
   **Answer:** It marks a class so Nest can manage it through its dependency injection container.
4. **Question:** How does a JavaScript controller declare constructor dependencies?  
   **Answer:** It uses `@Dependencies()` to list the provider tokens passed to its constructor.
5. **Question:** Why does JavaScript need explicit `@Dependencies()` metadata?  
   **Answer:** Runtime constructor parameter types are not available automatically, so Nest needs the tokens explicitly.
6. **Question:** Where must a provider be registered before Nest can inject it?  
   **Answer:** It must be in the current module's `providers` or exported by an imported module.
7. **Question:** When might a custom provider token be useful?  
   **Answer:** Use one for configuration values or to swap implementations behind a stable dependency.
8. **Question:** What is the default provider lifetime?  
   **Answer:** Providers are application-scoped singletons by default.

## 5. Route parameters, query values, and request bodies

1. **Question:** Where does a route parameter appear?  
   **Answer:** It is part of the URL path, for example `/cats/cat-7`.
2. **Question:** How does JavaScript bind an `id` route parameter?  
   **Answer:** Use a handler decorator such as `@Bind(Param('id'))`.
3. **Question:** What type do route and query values arrive as by default?  
   **Answer:** They arrive as strings and need parsing when another type is expected.
4. **Question:** Where do query string values appear in a URL?  
   **Answer:** After `?`, as key and value pairs such as `?page=2`.
5. **Question:** Which decorator reads a JSON request body?  
   **Answer:** `Body()` binds the request body to a handler argument.
6. **Question:** Does reading a request body validate its fields?  
   **Answer:** No. Binding reads the value; a pipe or schema must validate it.
7. **Question:** Why prefer focused request decorators over the full request object?  
   **Answer:** They expose only the needed value and reduce coupling to the HTTP adapter.
8. **Question:** What should be considered before returning an object to a client?  
   **Answer:** Return only fields the client is allowed to see and keep the response shape stable.

## 6. Validation and transformation with pipes

1. **Question:** At what point does a pipe run?  
   **Answer:** It runs before the route handler receives its bound value.
2. **Question:** What are the two common jobs of a pipe?  
   **Answer:** It can transform an input or reject it with an exception.
3. **Question:** How does `ParseIntPipe` handle a route value?  
   **Answer:** It converts a valid integer string to a number and rejects an invalid value.
4. **Question:** Where is a custom body pipe attached in the JavaScript example?  
   **Answer:** It is passed to `Body()` inside the route's `@Bind()` decorator.
5. **Question:** Why check that a body is an object and not an array?  
   **Answer:** An array is an object in JavaScript but does not have the expected named request fields.
6. **Question:** What should a body validator do with an invalid field?  
   **Answer:** Reject it with a client error before application logic uses it.
7. **Question:** Does input transformation determine whether a caller is authorized?  
   **Answer:** No. Authentication and authorization are separate checks.
8. **Question:** When can a schema validation library help?  
   **Answer:** It helps when a request has many fields or nested rules that are clearer in one schema.

## 7. Exceptions and HTTP error responses

1. **Question:** Which exception could represent a requested record that does not exist?  
   **Answer:** `NotFoundException` represents an unavailable requested record.
2. **Question:** What HTTP response does a `NotFoundException` normally produce?  
   **Answer:** It produces an HTTP 404 response.
3. **Question:** Name two other built-in HTTP exceptions.  
   **Answer:** `BadRequestException` and `ForbiddenException` are two examples.
4. **Question:** Why should the exception match what the client can do next?  
   **Answer:** The status tells the client whether to correct input, authenticate, request a different record, or stop.
5. **Question:** Why is a broad `try/catch` around every handler often unhelpful?  
   **Answer:** It can hide unexpected errors or replace useful framework handling with a vague response.
6. **Question:** What is one reason to define a custom exception?  
   **Answer:** A repeated domain failure may need a consistent status and public message.
7. **Question:** Which details should not appear in a public error response?  
   **Answer:** Do not expose stack traces, secrets, internal paths, or raw database details.
8. **Question:** When might an exception filter be useful?  
   **Answer:** Use one when multiple errors need a centralized response or logging policy.

## 8. Middleware and request lifecycle

1. **Question:** At what point does middleware run?  
   **Answer:** It runs before Nest executes guards and the route handler.
2. **Question:** What must middleware call to continue processing a request?  
   **Answer:** It must call `next()`.
3. **Question:** Name one reasonable use for middleware.  
   **Answer:** Adding a request identifier before the request reaches route handling.
4. **Question:** Why should request identifiers avoid copying user credentials?  
   **Answer:** Credentials in logs or response headers can expose account access.
5. **Question:** How can a module apply middleware to selected routes?  
   **Answer:** Its `configure()` method calls `consumer.apply(...).forRoutes(...)`.
6. **Question:** Which stage can decide whether a caller may reach a handler?  
   **Answer:** A guard makes that access decision.
7. **Question:** Which stages validate inputs and handle uncaught exceptions?  
   **Answer:** Pipes validate or transform values, and exception filters handle uncaught errors.
8. **Question:** Why keep middleware independent of feature business rules?  
   **Answer:** Middleware is a low-level request layer, while feature rules belong in providers and guards.

## 9. Guards, authentication, and authorization

1. **Question:** What question does authentication answer?  
   **Answer:** It establishes the identity associated with the request.
2. **Question:** What question does authorization answer?  
   **Answer:** It decides whether that identity may perform the requested action.
3. **Question:** When does a guard run in the request lifecycle?  
   **Answer:** It runs before the route handler and before bound pipes.
4. **Question:** Where does the guard get its role requirement?  
   **Answer:** It reads role metadata attached to the route handler.
5. **Question:** Who should populate `request.user`?  
   **Answer:** A verified authentication guard or trusted authentication middleware should populate it.
6. **Question:** Why should a role not be trusted from the request body?  
   **Answer:** A caller could set the field to a more privileged role.
7. **Question:** Which status is appropriate when the caller has no authenticated identity?  
   **Answer:** HTTP `401 Unauthorized` indicates missing or invalid authentication.
8. **Question:** Which status is appropriate when an authenticated caller lacks permission?  
   **Answer:** HTTP `403 Forbidden` indicates the identity lacks authorization.

## 10. Interceptors and response handling

1. **Question:** When can an interceptor run relative to a route handler?  
   **Answer:** It can run before the handler and process its result after the handler returns.
2. **Question:** What does `next.handle()` return to the interceptor?  
   **Answer:** It returns an observable representing the handler result.
3. **Question:** Which RxJS operator changes a successful result value?  
   **Answer:** `map()` transforms each emitted value.
4. **Question:** Where can an interceptor be applied?  
   **Answer:** It can be attached to a route, controller, or application globally.
5. **Question:** Why should a response wrapper be part of a documented API contract?  
   **Answer:** It changes the JSON shape clients receive and depend on.
6. **Question:** What does `finalize()` allow a timing interceptor to do?  
   **Answer:** It runs cleanup or timing logic when the observable completes or errors.
7. **Question:** Which layer is better suited to centralized HTTP error formatting?  
   **Answer:** An exception filter is intended for centralized error formatting.
8. **Question:** What should not be written to request timing logs?  
   **Answer:** Avoid logging credentials, private bodies, or response secrets.

## 11. Configuration and environment values

1. **Question:** Why keep environment-specific values outside source files?  
   **Answer:** The same code can run in different environments without committing private values.
2. **Question:** Which package provides `ConfigModule` and `ConfigService`?  
   **Answer:** The `@nestjs/config` package.
3. **Question:** What does `isGlobal: true` change?  
   **Answer:** It makes the configuration provider available across modules without importing the module repeatedly.
4. **Question:** What type does an environment variable have when first read?  
   **Answer:** It is a string.
5. **Question:** Why validate a port before starting the server?  
   **Answer:** Invalid values can prevent the listener from starting or bind it unexpectedly.
6. **Question:** What belongs in `.env.example`?  
   **Answer:** Safe sample values and the names of required settings, without real credentials.
7. **Question:** Where should production secrets be stored?  
   **Answer:** In protected environment configuration or a secret manager provided by the deployment platform.
8. **Question:** Why validate required configuration during startup?  
   **Answer:** The application can fail clearly before receiving traffic instead of failing during a request.

## 12. Persistence and data access patterns

1. **Question:** Why keep database calls out of a controller?  
   **Answer:** A provider or repository centralizes storage work and leaves the controller focused on HTTP.
2. **Question:** Which packages connect a Nest module to Mongoose?  
   **Answer:** `@nestjs/mongoose` and `mongoose`.
3. **Question:** Where should a production connection string come from?  
   **Answer:** Protected deployment configuration or a secret manager.
4. **Question:** What does a Mongoose schema describe?  
   **Answer:** It describes document fields and Mongoose-level validation behavior.
5. **Question:** How is a JavaScript model registered in a feature module?  
   **Answer:** Register the model name and schema with `MongooseModule.forFeature()`.
6. **Question:** How does `getModelToken()` help inject a model?  
   **Answer:** It returns the dependency injection token associated with a registered Mongoose model.
7. **Question:** What does `.lean()` return?  
   **Answer:** It returns plain JavaScript objects rather than hydrated Mongoose document instances.
8. **Question:** Why should arbitrary request objects not be passed directly into an update?  
   **Answer:** They may include fields the caller must not be allowed to change.

## 13. Logging and application lifecycle

1. **Question:** What does a logger context label help identify?  
   **Answer:** It shows which class or application area emitted the log entry.
2. **Question:** Name two Nest logger levels.  
   **Answer:** `log` and `warn` are two supported levels.
3. **Question:** What is one safe event to log from a service?  
   **Answer:** Record that a named operation started or completed, without logging its secret data.
4. **Question:** What is a provider lifecycle hook?  
   **Answer:** It is a method Nest calls during a provider or application lifecycle stage.
5. **Question:** When might `onModuleInit()` run?  
   **Answer:** It runs after the module's dependencies are initialized during application startup.
6. **Question:** What does `enableShutdownHooks()` allow the application to handle?  
   **Answer:** It lets Nest respond to operating system termination signals and run shutdown lifecycle hooks.
7. **Question:** Why is graceful shutdown useful?  
   **Answer:** It gives the process time to stop accepting work and release resources before it exits.
8. **Question:** Which values should not be written into application logs?  
   **Answer:** Avoid passwords, access tokens, cookies, and sensitive payloads.

## 14. Testing NestJS applications

1. **Question:** What is the difference between a unit test and an end-to-end test?  
   **Answer:** A unit test checks a small class or component; an end-to-end test exercises the running HTTP application boundary.
2. **Question:** Which Nest package provides `Test.createTestingModule()`?  
   **Answer:** The `@nestjs/testing` package.
3. **Question:** What does the `useValue` provider do in the example?  
   **Answer:** It supplies a fake implementation under the real provider's injection token.
4. **Question:** Why does the controller test avoid a real database?  
   **Answer:** The test isolates HTTP delegation logic and runs faster without external state.
5. **Question:** What application behavior does an HTTP-level test exercise?  
   **Answer:** It can verify routing, guards, pipes, serialization, and exception handling together.
6. **Question:** Why should a test close its module or application?  
   **Answer:** Closing releases connections, listeners, and other resources so the test process can exit cleanly.
7. **Question:** Name two failure cases worth testing.  
   **Answer:** Invalid input and unauthorized access are common cases to verify.
8. **Question:** Why assert public response behavior instead of private implementation details?  
   **Answer:** Public behavior is what clients depend on and remains meaningful when internals change.

## 15. Security, CORS, and API boundaries

1. **Question:** Name three controls at the API boundary.  
   **Answer:** Validate input, authenticate callers, and check permissions.
2. **Question:** What does Helmet help configure?  
   **Answer:** It sets HTTP security headers for the Express platform.
3. **Question:** What does CORS control in a browser?  
   **Answer:** It controls which web origins may read cross-origin responses.
4. **Question:** Does CORS authenticate a caller?  
   **Answer:** No. Authentication and authorization are separate controls.
5. **Question:** Why should credentialed CORS use explicit origins?  
   **Answer:** Explicit origins limit which browser applications can make credentialed cross-origin requests.
6. **Question:** Why can Express middleware not be assumed to work with Fastify?  
   **Answer:** The adapters use different middleware and plugin interfaces.
7. **Question:** When may CSRF protection be needed?  
   **Answer:** It may be needed when browser authentication uses cookies that are sent automatically.
8. **Question:** Which values should not appear in logs or public error responses?  
   **Answer:** Secrets, tokens, private payloads, file paths, and stack traces should be kept out.

## 16. Production readiness and deployment

1. **Question:** Why use the scripts from the generated project configuration?  
   **Answer:** They run the compiler and runtime settings selected for that JavaScript project.
2. **Question:** What does binding to `0.0.0.0` allow in a container environment?  
   **Answer:** It makes the server listen on interfaces reachable through the container network.
3. **Question:** Why validate the configured port before starting?  
   **Answer:** It prevents invalid or out-of-range values from reaching the listener.
4. **Question:** What do shutdown hooks let Nest do?  
   **Answer:** They let providers run cleanup when the process receives a termination signal.
5. **Question:** How does a readiness check differ from a liveness check?  
   **Answer:** Liveness reports whether the process should keep running; readiness reports whether it should receive traffic now.
6. **Question:** Name four useful production signals to monitor.  
   **Answer:** Request latency, error rate, memory use, and process restarts are useful signals.
7. **Question:** Why should a scaled application not rely on process memory for durable shared state?  
   **Answer:** Each instance has separate memory, and a restarted or different instance would not share that state.
8. **Question:** What should be tested before deploying a new artifact?  
   **Answer:** Test its clean build, environment configuration, health behavior, and graceful shutdown.
