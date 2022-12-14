# Building Your Domain Gateway With OpenAPI

> **TL;DR:** This article will explain what a domain gateway is, how to build one, and why do you want it.

## What is a domain gateway?

A domain gateway is a private case of the `api gateway` pattern. The repository [java-design-patterns](https://github.com/iluwatar/java-design-patterns/tree/master/api-gateway) refers to the `gateway pattern` as:

> With the Microservices pattern, a client may need data from multiple different microservices. If the client called each microservice directly, that could contribute to longer load times, since the client would have to make a network request for each microservice called. Moreover, having the client call each microservice directly ties the client to that microservice - if the internal implementations of the microservices change (for example, if two microservices are combined sometime in the future) or if the location (host and port) of a microservice changes, then every client that makes use of those microservices must be updated.
> 
> The intent of the API Gateway pattern is to alleviate some of these issues. In the API Gateway pattern, an additional entity (the API Gateway) is placed between the client and the microservices. The job of the API Gateway is to aggregate the calls to the microservices. Rather than the client calling each microservice individually, the client calls the API Gateway a single time. The API Gateway then calls each of the microservices that the client needs.

So what is a domain gateway? A domain gateway, like an API gateway, is a facade for your clients. Moreover, it allows aggregate calls to the backend into a single call for the clients. Last but not least, it allows replacing services in the backend without any awareness of our clients.

The following illustration can show a possible example of the domain gateway pattern:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670595692476/Kdg2FXLG_.png align="center")

If you are more of a class diagram kind of person, maybe this illustration will make more sense to you:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670668028317/TXL00rLLS.png align="center")

Note that a domain gateway doesn't have to facade calls for the clients. It can proxy them if the API is simple enough.

Now that we understand what a domain gateway is, here are a few Do's and Don'ts for this pattern.

### Do's

*   It should be simple. It should handle request proxy and/or request aggregations if needed.
    
*   It should maintain the API versions. It either forward the request to one or more APIs. Each of them can have a different version.
    
*   It should be lightweight and can scale up easily. If your domain gateway is not available, your entire domain is not available.
    

### Don'ts

*   It should not handle any business logic whatsoever. For example, sending emails, generating files, etc.
    
*   It should not store any business logic or object models in the database. This service should be completely stateless and have no knowledge of business logic.
    

## Ok, I'm convinced...

I hope that I convinced you by now that a domain gateway is useful. If you already read my article [**How to write robust REST API with OpenAPI**](https://yonatankarp.com/how-to-write-robust-rest-api-with-openapi) you probably know I'm a fan of OpenAPI specs for your service.

The problem introduced in my previous article is: how can I generate many specs at once?

## How do I build it?

![selective focus photography of mechanics tool lot](https://images.unsplash.com/photo-1530124566582-a618bc2615dc?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1000&q=80 align="left")

### Tech Stack

We will use the following tech stack:

*   [Kotlin](https://kotlinlang.org/)
    
*   [Spring Boot](https://spring.io/projects/spring-boot) - At the time of writing this article, SpringBoot 3 is still not supported by the OpenAPI generator. Thus we will use the latest Spring Boot 2
    
*   [Gradle Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html)
    
*   [Retrofit](https://square.github.io/retrofit/)
    
*   [OpenAPI](https://www.openapis.org/)
    

### API Specs

For the sake of simplicity, let us assume that we have only 2 services in our domain. Each of them services a single endpoint that is unrelated to the other. The illustration below should show an example of how our client will integrate with our domain:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670595471439/58xR2RlDW.png align="center")

If we look at the service structure of our domain it would look something like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670667943709/OpCJo0he5.png align="center")

Let's define our specs now.

#### Hello Service

Our `Hello` API will expose a single endpoint: `/hello/{name}`. This endpoint will answer the client with `Hello <NAME>` (e.g. `Hello Yonatan`).

```yaml
openapi: 3.0.3
info:
  title: Hello API
  description: A service greeting with Hello World
  version: 1.0.0

tags:
  - name: Hello

paths:

  /hello/{name}:
    get:
      operationId: hello
      summary: Returns hello + user name
      tags:
        -  Hello
      parameters:
        - in: path
          name: name
          schema:
            type: string
          required: true
      responses:
        "200":
          description: Successfully returning hello + user name to the client.
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/HelloResponse"

components:
  schemas:
    HelloResponse:
      type: object
      required:
        - value
      properties:
        value:
          type: string
          example: "Hello world"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670605520716/-zNnILEbb.png align="center")

#### Goodbye Service

Our `Goodbye` API is exactly like the `Hello` API. it will contain a single endpoint: `/goodbye/{name}`. This endpoint, just like `Hello` API would answer the client with `Goodbye <NAME>` (e.g. `Goodbye Yonatan`).

```yaml
openapi: 3.0.3
info:
  title: Goodbye API
  description: A service greeting with Hello World
  version: 1.0.0

tags:
  - name: Goodbye

paths:

  /goodbye/{name}:
    get:
      operationId: goodbye
      summary: Returns goodbye + user name
      tags:
        -  Goodbye
      parameters:
        - in: path
          name: name
          schema:
            type: string
          required: true
      responses:
        "200":
          description: Successfully returning goodbye + name to the client.
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/GoodbyeResponse"

components:
  schemas:
    GoodbyeResponse:
      type: object
      required:
        - value
      properties:
        value:
          type: string
          example: "Goodbye world"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670605551084/UB_TW4lZg.png align="center")

#### Greeting Service

The greeting API is a facade of the 2 services above. It should include 2 API endpoints:

*   `/hello/{name}`
    
*   `/goodbye/{name}`
    

```yaml
openapi: 3.0.3
info:
  title: Gateway API
  description: A service greets the user
  version: 1.0.0

tags:
  - name: Gateway

paths:

  /hello/{name}:
    get:
      operationId: hello
      summary: Returns hello + user name
      tags:
        - Gateway
      parameters:
        - in: path
          name: name
          schema:
            type: string
          required: true
      responses:
        "200":
          description: Successfully returning hello + user name to the client.
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/HelloResponse"

  /goodbye/{name}:
    get:
      operationId: goodbye
      summary: Returns goodbye + user name
      tags:
        - Gateway
      parameters:
        - in: path
          name: name
          schema:
            type: string
          required: true
      responses:
        "200":
          description: Successfully returning goodbye + name to the client.
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/GoodbyeResponse"

components:
  schemas:
    HelloResponse:
      type: object
      required:
        - value
      properties:
        value:
          type: string
          example: "Hello world"

    GoodbyeResponse:
      type: object
      required:
        - value
      properties:
        value:
          type: string
          example: "Goodbye world"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670605578406/UgCyRHnfS.png align="center")

We will locate all the above specs in our `/resource/api` directory with the following names:

*   `hello-api.yaml`
    
*   `goodbye-api.yaml`
    
*   `gateway-api.yaml`
    

### Generate multiple specs

We will reuse the same setup we used in the previous article. But we would like to amend it so we can generate an unlimited amount of specs. If you're missing an explanation on how to configure the OpenAPI Gradle plugin, please go and read my previous article.

The 1st step in our journey is introducing a new class into our Gradle build script. This class would hold all the required information about the spec.

```kotlin
/**
 * A class represents a specific spec to generate.
 */ 
data class ApiSpec(
    val name: String,
    val taskName: String,
    val directoryPath: String,
    val outputDir: String,
    val specFileName: String,
    val generatorType: String,
    val packageName: String,
    val modelPackageName: String,
    val config: Map<String, String>,
    val templateDir: String? = null
)
```

Now, we will create a list of all the specs we would like to generate in our `build.gradle.kts`:

```kotlin
/**
 * List of all api specs to generate
 */
val supportedApis = listOf(
    ApiSpec(
        name = "Gateway API",
        taskName = "generateGatewayApi",
        directoryPath = apiDirectoryPath,
        templateDir = "$apiDirectoryPath/templates/server",
        outputDir = "$openApiGenerateOutputDir/domain-gateway",
        specFileName = "gateway-api.yaml",
        generatorType = "kotlin-spring",
        packageName = "com.yonatankarp.gateway.openapi.v1",
        modelPackageName = "com.yonatankarp.gateway.openapi.v1.models",
        config = mapOf(
            "dateLibrary" to "java8",
            "interfaceOnly" to "true",
            "implicitHeaders" to "true",
            "hideGenerationTimestamp" to "true",
            "useTags" to "true",
            "documentationProvider" to "none"
        )
    ),
    ApiSpec(
        name = "Hello API",
        taskName = "generateHelloApi",
        directoryPath = apiDirectoryPath,
        outputDir = "$openApiGenerateOutputDir/domain-gateway",
        specFileName = "hello-api.yaml",
        generatorType = "kotlin",
        packageName = "com.yonatankarp.hello.openapi.v1_current",
        modelPackageName = "com.yonatankarp.hello.openapi.v1_current.models",
        config = mapOf(
            "dateLibrary" to "java8",
            "interfaceOnly" to "true",
            "implicitHeaders" to "true",
            "hideGenerationTimestamp" to "true",
            "useTags" to "true",
            "documentationProvider" to "none",
            "serializationLibrary" to "jackson",
            "useCoroutines" to "true",
            "library" to "jvm-retrofit2"
        )
    ),
    ApiSpec(
        name = "Goodbye API",
        taskName = "generateGoodbyeApi",
        directoryPath = apiDirectoryPath,
        outputDir = "$openApiGenerateOutputDir/domain-gateway",
        specFileName = "goodbye-api.yaml",
        generatorType = "kotlin",
        packageName = "com.yonatankarp.goodbye.openapi.v1_current",
        modelPackageName = "com.yonatankarp.goodbye.openapi.v1_current.models",
        config = mapOf(
            "dateLibrary" to "java8",
            "interfaceOnly" to "true",
            "implicitHeaders" to "true",
            "hideGenerationTimestamp" to "true",
            "useTags" to "true",
            "documentationProvider" to "none",
            "serializationLibrary" to "jackson",
            "useCoroutines" to "true",
            "library" to "jvm-retrofit2"
        )
    )
)
```

**Note:** If you noticed, all of our specs are generated into the same output directory. The reason for that is that OpenAPI generates some infrastructure classes that are used by the generated code. If we will not generate them in the same directory, the `sourceDir` will include duplications of classes in the same packages and the code will not compile.

You can see that we're still separating our APIs by package name in the generated code:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670669752006/tuuB0fh8d.png align="center")

The next steps are the most important parts. We will define some generic functions that will do the following:

*   register a new task for each spec to generate the code.
    
*   add the generated code to the source set.
    
*   make the `clean` task finalized by the generation task of the spec.
    
*   make `compileKotlin` depends on the generation task of the spec.
    

Let's start.

We will start by creating the tasks for each of our specs under the `openapi tools` group:

```kotlin
// Iterate over the api list and register them as generator tasks
supportedApis.forEach { api ->
    tasks.create(api.taskName, GenerateOpenApiTask::class) {
        group = "openapi tools"
        description = "Generate the code for ${api.name}"

        generatorName.set(api.generatorType)
        inputSpec.set("${api.directoryPath}/${api.specFileName}")
        outputDir.set(api.outputDir)
        apiPackage.set(api.packageName)
        modelPackage.set(api.modelPackageName)
        configOptions.set(api.config)
        api.templateDir?.let {this.templateDir.set(it)}
    }
}
```

The next step is adding our generated code from the previous tasks into our `sourceSet`. We're using the 1st element in the list as all of our files are generated into the same output directory.

```kotlin
supportedApis.first().let {
    sourceSets[SourceSet.MAIN_SOURCE_SET_NAME].java {
        srcDir("${it.outputDir}/src/main/kotlin")
        exclude(
            "**/ApiClient.kt",
            "**/OAuth.kt",
            "**/OAuthOkHttpClient.kt"
        )
    }
}
```

**Note:** we're including the `ApiClient.kt`, `OAuth.kt,` and `OAuthOkHttpClient.kt` as currently there is a bug in the generator. This bug doesn't allow generating Retrofit clients with Jackson as the serialization library. I have [opened a pull request](https://github.com/OpenAPITools/openapi-generator/pull/14239) for the fix on the OpenApi generator's repository. If the pull request was merged, you can remove the `exclude` part from your project.

The last step would be to ensure `clean` tasks and `compileKotlin` are playing well without new tasks. We will create a new task called `cleanGeneratedCodeTask` that would simply delete all generated code whenever we run the `clean` task. Moreover, we want to ensure that before we build our code, all code generation tasks have been executed.

```kotlin
tasks {
    register("cleanGeneratedCodeTask") {
        description = "Removes generated Open API code"
        group = "openapi tools"

        doLast {
            logger.info("Cleaning up generated code")
            File(openApiGenerateOutputDir).deleteRecursively()
        }
    }

    clean {
        dependsOn("cleanGeneratedCodeTask")
        supportedApis.forEach { finalizedBy(it.taskName) }
    }

    compileKotlin {
        supportedApis.forEach { dependsOn(it.taskName) }
    }
}
```

As you can see, now all of our tasks are available for use in Gradle!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670608196771/om9SajUce.png align="center")

The last thing we need to do is add some dependencies to our `build.gradle.kts` to ensure our code can be compiled.

```kotlin
dependencies {
    api("com.squareup.retrofit2:retrofit:$retrofitVersion")
    api("com.squareup.retrofit2:converter-jackson:$retrofitVersion")
    api("com.squareup.okhttp3:logging-interceptor:$okHttpVersion")
}
```

## Using our generated code

### Domain Gateway Controller

The domain gateway controller implementation is very simple. It's a Spring `@RestController` that implements a given interface. Thus our code would look like that:

```kotlin
@RestController
class DomainGatewayController : GatewayApi {

    override fun hello(
        name: String
    ): ResponseEntity<HelloResponse> = TODO()

    override fun goodbye(
        name: String
    ): ResponseEntity<GoodbyeResponse> = TODO()
}
```

### Clients

As mentioned above, currently, there is a bug in the OpenAPI generator. This bug prevents us from using the auto-generated client. Thus, we will have to define the Retrofit clients manually.

we will start by defining our `OkHttp` client. Note that I'm setting the logger interceptor level to `BODY`. It's great for debugging, but **NEVER** use it on production. It will log all your request and response bodies. This may lead to the exposure of sensitive information in your logs. If you still want to log your request/response body. You need to build a custom interceptor.

```kotlin
private fun okHttpClient() =
    OkHttpClient
        .Builder()
        .addInterceptor(HttpLoggingInterceptor()
            .apply { level = BODY })
        .build()
```

The next step is to define the Jackson converter factory. The factory needs the `ObjectMapper` of Spring context. Do not create a new `ObjectMapper` for it! I spent about half of my day at work trying to debug this issue.

```kotlin
@Bean
fun jacksonConverterFactory(objectMapper: ObjectMapper): JacksonConverterFactory =
    JacksonConverterFactory.create(objectMapper)
```

Next, we will define beans for our 2 API clients. Keep in mind that while we're using the convertor from above, we are injecting a generic `Factory`. We're doing so, to make it easier for ourselves to replace the library future.

```kotlin
@Bean
@Suppress("SpringJavaInjectionPointsAutowiringInspection")
fun helloApiClient(converterFactory: Factory): HelloApi =
    Retrofit
        .Builder()
        .addConverterFactory(converterFactory)
        .baseUrl("http://hello-service:8080")
        .client(okHttpClient())
        .build()
        .create(HelloApi::class.java)

@Bean
@Suppress("SpringJavaInjectionPointsAutowiringInspection")
fun goodbyeApiClient(converterFactory: Factory): GoodbyeApi =
    Retrofit
        .Builder()
        .addConverterFactory(converterFactory)
        .baseUrl("http://goodbyte-service:8789")
        .client(okHttpClient())
        .build()
        .create(GoodbyeApi::class.java)
```

That's all, from this point forward we can use our client as follows:

```kotlin
helloApi.hello("Yonatan")
goodbyeApi.goodbye("Yonatan")
```

## Conclusion

In this article, I've explained the benefits of aggregating your services into a domain gateway. We used the power of OpenAPI to ensure the robustness of our API both publically (for our clients) and internally (between our services).

All code examples in this article are available in my GitHub account at this repository: [https://github.com/yonatankarp/domain-gateway-demo](https://github.com/yonatankarp/domain-gateway-demo)

## More Information

*   [API Versioning with Kotlin and Spring Boot](https://medium.com/towardsdev/api-versioning-with-kotlin-and-spring-boot-ef9e08214526) by Mariusz Sołtysiak. Amazing article by a colleague of mine about API versioning that is used in their domain gateway.
    
*   [Java Design Patterns - API Gateway](https://java-design-patterns.com/patterns/api-gateway/)