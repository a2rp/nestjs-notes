# 16. Production readiness and deployment

[Back to notes index](../README.md)

| [Previous: Security, CORS, and API boundaries](./15-security-cors-and-api-boundaries.md) | [Notes index](../README.md) | [Next: All code samples](./98-all-code-samples.md) |
| --- | --- | --- |

## Prepare the generated project for a release

Use the build and start scripts generated for the selected JavaScript setup. Run the production build in a clean checkout and confirm that the runtime command uses the generated Babel or bundler configuration:

~~~sh
npm ci
npm run build
npm run start:prod
~~~

The exact scripts can vary with Nest CLI versions and project options. Review `package.json` before deployment instead of assuming every generated project has identical commands.

## Listen on the deployment interface

In a container or hosted environment, the server usually needs to bind to the configured port and an address reachable by the platform:

~~~javascript
const port = Number(process.env.PORT ?? 3000)
await app.listen(port, '0.0.0.0')
~~~

Validate the port before using it. Keep local development and production values in environment configuration, and make sure the deployed process has the required secrets and network access.

## Shut down cleanly

Enable Nest shutdown hooks so termination signals can trigger provider cleanup:

~~~javascript
const app = await NestFactory.create(AppModule)
app.enableShutdownHooks()
await app.listen(port, '0.0.0.0')
~~~

A deployment platform may send a termination signal before stopping the container. Stop accepting new work, complete or cancel in-flight requests within the allowed time, and close resources owned by the application.

## Add health checks and monitoring

A liveness check answers whether the process can continue running. A readiness check answers whether it should receive new traffic, including whether required dependencies are usable. Keep health responses small and avoid exposing connection strings or internal topology.

Monitor request latency, error rates, memory, CPU, restarts, and dependency health. Use structured logs with request identifiers. Configure timeouts and body limits at the application and gateway boundaries.

## Keep instances easy to replace

A horizontally scaled API should not rely on process memory as its durable data store. Store shared state in a database or a purpose-built external service. Keep startup repeatable, deploy versioned artifacts, and test configuration and shutdown behavior before rollout.

## Check what you learned

1. Why use the scripts from the generated project configuration?
2. What does binding to `0.0.0.0` allow in a container environment?
3. Why validate the configured port before starting?
4. What do shutdown hooks let Nest do?
5. How does a readiness check differ from a liveness check?
6. Name four useful production signals to monitor.
7. Why should a scaled application not rely on process memory for durable shared state?
8. What should be tested before deploying a new artifact?

## References

- [NestJS deployment](https://docs.nestjs.com/deployment)
- [Lifecycle events and shutdown](https://docs.nestjs.com/fundamentals/lifecycle-events)
- [Health checks](https://docs.nestjs.com/recipes/terminus)
- [NestJS performance](https://docs.nestjs.com/techniques/performance)
