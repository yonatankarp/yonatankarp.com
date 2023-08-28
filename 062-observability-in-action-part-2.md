---
title: "Observability in Action Part 2: Enhancing Your Codebase with OpenTelemetry"
subtitle: "A Step-by-Step Guide to Building a Kotlin Service for Cat Facts API"
slug: observability-in-action-part-2-enhancing-your-codebase-with-opentelemetry
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: kotlin, observability, opentelemetry, devops, springboot
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/1MwB2BXuUMI/upload/b17c33f58c51320d329cb4820eaabf80.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Boost Your Application Performance: A Guide to OpenTelemetry Integration"
seoDescription: "Elevate your codebase with OpenTelemetry. Create a Spring service, link to a database, and deploy using Docker in this step-by-step guide."
seriesSlug: spring-boot
# Table of contents
enableToc: true
saveAsDraft: true
---

> **TL;DR:** Enhancing your codebase with OpenTelemetry involves building a service that returns cat facts, saves them to a database, and sends them back to the caller. This is demonstrated using a Spring-based service, loading facts into a request context, storing and reading facts, Dockerizing the project, and setting up docker-compose for easy deployment.

Welcome to my series on Observability in Action. In this series, I explore various aspects of enhancing your codebase with modern observability techniques. If you're new to the series, I highly encourage you to check out our previous articles to get a comprehensive understanding of the topics covered:

### Series Outline

* Part 1 - Build Client Library

* Part 2 - Build the Service ⬅ You are here

* Part 3 - Instrument the Service

* Part 4 - Instrument a Spring Filter

* Part 5 - Integrate the OpenTelemetry Collector


All code examples for this series are available on GitHub:

* All code examples related to this service are available [here](https://github.com/yonatankarp/cat-fact-service/tree/business-logic-implementation) on the branch `business-logic-implementation`


![A cat wearing a hard hat with the word cat on it](https://cdn.hashnode.com/res/hashnode/image/upload/v1693144413338/RmtSqhi4_.avif?auto=format align="center")

## Building the Service

The service includes the following features:

* Each request returns a specified number of cat facts (between 1-10) using the `cat-fact-client` library, If not specified, the default is 5 facts

* Each fact is saved to the database unless it's already stored.

* The retrieved facts are then sent back to the caller.


You might wonder, ***"When would I ever need this?!"*** This is primarily a demonstrative use case. Imagine a scenario where instead of accessing a facts API, you're calling a user management service to ensure a user's context is saved in the request.

Various methods can deliver this functionality. Here, we will utilize Spring's `@Scope` for a bean that's instantiated for every new service request.

### Adding Dependencies

To integrate our library, append the following dependency to the project:

```kotlin
repositories {
    maven {
        url = uri("https://maven.pkg.github.com/ForkingGeniuses/cat-fact-client")
        credentials {
            username = project.findProperty("gpr.user")?.toString() ?: System.getenv("GITHUB_ACTOR")
            password = project.findProperty("gpr.key")?.toString() ?: System.getenv("GITHUB_TOKEN")
        }
    }
}
```

Ensure you have a PAT (Personal Access Token) with the `read:packages` privilege to access the library. Detailed instructions on generating a PAT can be found [here](https://docs.github.com/en/packages/learn-github-packages/about-github-packages#authenticating-to-github-packages).

Subsequently, insert the dependency into the project:

```kotlin
dependencies {
    implementation("com.yonatankarp:cat-fact-client:0.2.0")
}
```

After refreshing your Gradle project, if configured appropriately, the library should be available.

### Loading the Facts

![loading screen nyan cat](https://cdn.hashnode.com/res/hashnode/image/upload/v1693144480360/wa0kqoGT5.jpeg?auto=format align="left")

We begin by defining the `RequestContext` interface, which will load facts for each request. Spring will use its implementation, `RequestContextImpl`, to inject facts into the controller.

```kotlin
/**
 * Provides facts about cats into the request context.
 *
 * This interface is used by SpringBoot to inject the user context into
 * the controller for each call made for the service.
 */
interface RequestContext {
    var facts: Set<Fact>?
}

/**
 * The implementation of the [RequestContext] interface. Used by
 * SpringBoot to populate the facts into the request context.
 */
open class RequestContextImpl(
    override var facts: Set<Fact>? = null
) : RequestContext
```

We will now configure our context to be included with each request arriving at the service. Note that we're using Jackson's `ObjectMapper` for the `cat-fact-client` library, as this is the default serialization library for Spring. Moreover, the `@Scope` annotation requires a none-suspended function, and therefore we have to use the `runBlocking{}` to bridge between the library and our service.

```kotlin
@Configuration
class ApplicationConfiguration {
    /**
     * Creates a new instance of the [CatFactProvider] that will be 
     * used to fetch the facts about cats.
     */
    @Bean
    fun catFactProvider(objectMapper: ObjectMapper): CatFactProvider =
        CatFactFactory.getInstance(ProviderType.API, objectMapper)

    /**
     * Creates a new instance of the [RequestContext] that will be 
     * used to inject the facts into the request context.
     */
    @Bean
    @Scope(
        WebApplicationContext.SCOPE_REQUEST,
        proxyMode = ScopedProxyMode.INTERFACES
    )
    fun requestContext(
        catFactProvider: CatFactProvider
    ): RequestContext = runBlocking {
        RequestContextImpl(catFactProvider.get(getMaxFactsNumber()))
    }
    
    /**
     * Returns the maximum number of facts that should be returned 
     * to the caller, or the default value if not specified.
     */
    private fun getMaxFactsNumber(): Int {
        val servletRequestAttributes =
            RequestContextHolder.getRequestAttributes() as ServletRequestAttributes
        
        return servletRequestAttributes
            .request
            .getParameterValues("max")
            ?.first()
            ?.toInt()
            ?.coerceIn(MIN_FACTS_NUMBER, MAX_FACTS_NUMBER)
            ?: DEFAULT_FACTS_NUMBER
    }

    companion object {
        private const val DEFAULT_FACTS_NUMBER = 5
        private const val MIN_FACTS_NUMBER = 1
        private const val MAX_FACTS_NUMBER = 10
    }
}
```

### Storing and Reading Facts

![russian blue cat in brown cardboard box](https://cdn.hashnode.com/res/hashnode/image/upload/v1693144536917/zRFtYNKlr.avif?auto=format align="left")

With the facts now in the request context, they can be utilized in our controller.

```kotlin
@RestController
class CatFactController(
    // Would be automatically injected by Spring
    private val requestContext: RequestContext,
    private val catFactService: CatFactService,
) {
    @GetMapping("/api/v1/cat/facts")
    suspend fun getCatFacts(): ResponseEntity<FactsResponse> {
        val facts = requestContext.facts ?: throw RuntimeException("Could not read facts")
        catFactService.storeFacts(facts)
        return ok(facts.toResponse())
    }
}

private fun Set<Fact>.toResponse() = FactsResponse(this)

data class FactsResponse(val facts: Set<Fact>)
```

Our relatively straightforward service merely iterates over the available facts, storing each in turn.

```kotlin
@Service
class CatFactService(private val repository: CatFactRepository) {
    suspend fun storeFacts(facts: Set<Fact>) =
        facts.forEach { repository.storeFacts(it) }
}
```

The remaining step is saving our facts to the database. We'll use [Flyway](https://flywaydb.org/) for database migrations and [JOOQ](https://www.jooq.org/) for database interaction. Begin by establishing a table in the database.

Add the following SQL script to the `src/main/resources/db/migration/V1.0.0__init_db.sql` folder:

```sql
CREATE TABLE cat_facts (
    hash INT PRIMARY KEY,
    fact TEXT NOT NULL
);
```

The facts lack unique identifiers, so we'll use the fact's hash to check if it's already in the database. Although this method isn't ideal, it suits our needs.

Lastly, we'll develop a repository to save the facts in the database. This straightforward repository will add facts and skip those already present.

```kotlin
@Repository
class CatFactRepository(private val jooq: DSLContext) {
    suspend fun storeFacts(fact: Fact) =
        with(Tables.CAT_FACTS) {
            jooq.insertInto(this, HASH, FACT)
                .values(fact.hashCode(), fact.value)
                .onConflict(HASH)
                .doNothing()
                .execute()
        }
}
```

### Dockerizing the Project

![blue and red cargo ship on sea during daytime](https://cdn.hashnode.com/res/hashnode/image/upload/v1693144577802/WMPmsPL5_.avif?auto=format align="left")

To replicate our services' production behavior, we'll use Docker to create an image, subsequently testing our service.

```dockerfile
FROM --platform=linux/x86_64 eclipse-temurin:17-jre-alpine

ENV APP_BASE="/home" \
    APP_NAME="cat-fact-service" \
    SERVER_PORT="8080"

EXPOSE ${SERVER_PORT}

# Install helper tools for debugging
RUN apk update && apk upgrade && apk add curl openssl gcompat bash busybox-extras iputils

RUN mkdir -p ${APP_BASE}/${APP_NAME}

COPY build/libs/${APP_NAME}*.jar ${APP_BASE}/${APP_NAME}.jar

CMD java -jar ${APP_BASE}/${APP_NAME}.jar
```

To verify your project's functionality, execute:

```bash
$ ./gradlew assemble
$ docker build -t cat-fact-service .
```

You should be output similar to the following:

```bash
[+] Building 2.3s (10/10) FINISHED                                                                                                                                                                            docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                                                                                                          0.0s
 => => transferring dockerfile: 453B                                                                                                                                                                                          0.0s
 => [internal] load .dockerignore                                                                                                                                                                                             0.0s
 => => transferring context: 2B                                                                                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/eclipse-temurin:17-jre-alpine                                                                                                                                              1.5s
 => [auth] library/eclipse-temurin:pull token for registry-1.docker.io                                                                                                                                                        0.0s
 => [1/4] FROM docker.io/library/eclipse-temurin:17-jre-alpine@sha256:e90e0d654765ab3ae33f5c5155daafa4a907d0d738ce98c3be8f402a8edcee2b                                                                                        0.0s
 => [internal] load build context                                                                                                                                                                                             0.6s
 => => transferring context: 82.75MB                                                                                                                                                                                          0.6s
 => CACHED [2/4] RUN apk update && apk upgrade && apk add curl openssl gcompat bash busybox-extras iputils                                                                                                                    0.0s
 => CACHED [3/4] RUN mkdir -p /home/cat-fact-service                                                                                                                                                                          0.0s
 => [4/4] COPY build/libs/cat-fact-service*.jar /home/cat-fact-service.jar                                                                                                                                                    0.1s
 => exporting to image                                                                                                                                                                                                        0.1s
 => => exporting layers                                                                                                                                                                                                       0.1s
 => => writing image sha256:0b65dc5d8e98c9b095ce4bd38ab28eae8320ff1cfb089b59b039d3753cf6ec45                                                                                                                                  0.0s
 => => naming to docker.io/library/cat-fact-service
```

### Setting up docker-compose

While the service is operational, the database setup remains. Docker-compose will establish both the database and the service.

At your project's root, create a new file named `docker-compose.yml` with the following content:

```yaml
version: '3'

services:
  cat-fact-service:
    container_name: cat-fact-service
    networks:
      - proxynet
    build: ../..
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    environment:
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_NAME=facts
      - DB_USER=postgres
      - DB_PASSWORD=secret

  postgres:
    container_name: cat-fact-service-postgres
    networks:
      - proxynet
    image: postgres:14
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: facts
    ports:
      - "5432:5432"

networks:
  proxynet:
    name: cat-fact-service-network
```

### Running the Service

You can now launch and assess the service by executing:

```bash
$ docker-compose up
```

Once Spring notifies you that the service is operational, access it via your browser at [http://localhost:8080/api/v1/cat/facts](http://localhost:8080/api/v1/cat/facts).

If executed correctly, the response should resemble:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692880679471/1e0a52c3-3bc3-406f-bda3-e181ab726a05.png align="center")

To retrieve a custom number of facts, use the `max` query parameter. For ten facts, navigate to [http://localhost:8080/api/v1/cat/facts?max=10](http://localhost:8080/api/v1/cat/facts?max=10).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692880690525/fa785133-1e2e-432e-a717-135d2442e9d5.png align="center")

Inspecting the database will confirm the successful storage of facts.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692880698423/e0a2e4e9-5906-4ea0-86e2-1e580b31bb0b.png align="center")

Our service logic is ready!

![International Cat Day](https://cdn.hashnode.com/res/hashnode/image/upload/v1693144765113/MOX38aF5I.webp?auto=format align="center")

Alternatively, you can pull the docker image that I have created from [GHCR](https://docs.github.com/de/packages/working-with-a-github-packages-registry/working-with-the-container-registry) by changing your docker-compose file as follows:

```yaml
version: '3'

services:
  cat-fact-service:
    container_name: cat-fact-service
    image: ghcr.io/yonatankarp/cat-fact-service:latest
    networks:
      - proxynet
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    environment:
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_NAME=facts
      - DB_USER=postgres
      - DB_PASSWORD=secret

  postgres:
    container_name: cat-fact-service-postgres
    networks:
      - proxynet
    image: postgres:14
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: facts
    ports:
      - "5432:5432"

networks:
  proxynet:
    name: cat-fact-service-network
```

## Conclusion

In this part of the Observability in Action series, we demonstrated how to build a Spring-based service that returns cat facts, saves them to a database, and sends them back to the caller. We covered loading facts into a request context, storing and reading facts, Dockerizing the project, and setting up docker-compose for easy deployment. Stay tuned for the upcoming parts of the series, where we will explore instrumenting the service, integrating OpenTelemetry Collector, and more.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Acknowledgments

* [Mariusz Sołtysiak](https://medium.com/@mariuszsoltysiak) - for moral support, review, and suggestions while writing this series of articles.
