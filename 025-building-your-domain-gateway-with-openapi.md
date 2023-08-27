---
title: "Building Your Domain Gateway With OpenAPI"
subtitle: ""
slug: building-your-domain-gateway-with-openapi
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: gradle, kotlin, springboot, openapi, gateway-api
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670595100693/5hdHlH6p1.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Build a Domain Gateway: Aggregating Microservices with OpenAPI"
seoDescription: "Learn how to build a Domain Gateway using OpenAPI to aggregate microservices, ensure API robustness, and improve communication between services."
seriesSlug: jvm
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** This article explains what a domain gateway is, how to build one, and why you would want it.

## What is a Domain Gateway?

A domain gateway is a private case of the API gateway pattern. The repository [java-design-patterns](https://github.com/iluwatar/java-design-patterns/tree/master/api-gateway) defines the gateway pattern as follows:

> With the Microservices pattern, a client may need data from multiple different microservices. If the client called each microservice directly, that could contribute to longer load times, since the client would have to make a network request for each microservice called. Moreover, having the client call each microservice directly ties the client to that microservice - if the internal implementations of the microservices change (for example, if two microservices are combined sometime in the future) or if the location (host and port) of a microservice changes, then every client that makes use of those microservices must be updated.
> 
> The intent of the API Gateway pattern is to alleviate some of these issues. In the API Gateway pattern, an additional entity (the API Gateway) is placed between the client and the microservices. The job of the API Gateway is to aggregate the calls to the microservices. Rather than the client calling each microservice individually, the client calls the API Gateway a single time. The API Gateway then calls each of the microservices that the client needs.

A domain gateway, like an API gateway, acts as a facade for clients. It allows aggregating calls to the backend into a single call for the clients. Additionally, it enables replacing backend services without impacting the clients.

The following illustration shows a possible example of the domain gateway pattern:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670595692476/Kdg2FXLG_.png align="center")

If you prefer a class diagram, this illustration might make more sense:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670668028317/TXL00rLLS.png align="center")

Note that a domain gateway doesn't have to facade calls for the clients; it can proxy them if the API is simple enough.

Now that we understand what a domain gateway is, here are a few do's and don'ts for this pattern.

### Do's

* Keep it simple. It should handle request proxying and/or request aggregation if needed.
    
* Maintain API versions. It should forward the request to one or more APIs, each with potentially different versions.
    
* Ensure it is lightweight and can scale easily. If your domain gateway is unavailable, your entire domain will be unavailable.
    

### Don'ts

* Handle any business logic. For example, tasks such as sending emails or generating files should not be performed by the domain gateway.
    
* Store any business logic or object models in the database. The domain gateway should be completely stateless and have no knowledge of business logic.
    

## Ok, I'm Convinced...

I hope I have convinced you that a domain gateway is useful. If you have already read my article [**How to Write Robust REST API with OpenAPI**](https://yonatankarp.com/how-to-write-robust-rest-api-with-openapi), you probably know that I'm a fan of using OpenAPI specs for your service.

The problem introduced in my previous article is: how can I generate many specs at once?

## How Do I Build It?

![Selective focus photography of mechanics tool lot](https://images.unsplash.com/photo-1530124566582-a618bc2615dc?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1000&q=80 align="left")

### Tech Stack

We will use the following tech stack:

* [Kotlin](https://kotlinlang.org/)
    
* [Spring Boot](https://spring.io/projects/spring-boot) - At the time of writing this article, Spring Boot 3 is not yet supported by the OpenAPI generator, so we will use the latest Spring Boot 2.
    
* [Gradle Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html)
    
* [Retrofit](https://square.github.io/retrofit/)
    
* [OpenAPI](https://www.openapis.org/)
    

### API Specs

For simplicity, let's assume that we have only two services in our domain. Each service serves a single endpoint that is unrelated to the other. The illustration below shows an example of how our client will integrate with our domain:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670595471439/58xR2RlDW.png align="center")

If we look at the service structure of our domain, it would look something like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670667943709/OpCJo0he5.png align="center")

Let's define our specs now.

#### Hello Service

Our `Hello` API will expose a single endpoint: `/hello/{name}`. This endpoint will respond to the client with `Hello <NAME>` (e.g., `Hello Yonatan`).

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
        - Hello
      parameters:
        - in: path
          name: name
          schema:
            type: string
          required: true
      responses:
        "200":
          description: |
              Successfully returning hello + user name to the client.
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

Our `Goodbye` API is exactly like the `Hello` API. It contains a single endpoint: `/goodbye/{name}`. This endpoint, just like the `Hello` API, responds to the client with `Goodbye <NAME>` (e.g., `Goodbye Yonatan`).

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
        - Goodbye
      parameters:
        - in: path
          name: name
          schema:
            type: string
          required: true
      responses:
        "200":
          description: |
              Successfully returning goodbye + name to the client.
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

The greeting API acts as a facade for the two services above. It includes two API endpoints:

* `/hello/{name}`
    
* `/goodbye/{name}`
    

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
          description: |
              Successfully returning hello + user name to the client.
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
          description: |
              Successfully returning goodbye + name to the client.
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

* `hello-api.yaml`
    
* `goodbye-api.yaml`
    
* `gateway-api.yaml`
    

### Generating Multiple Specs

We will reuse the same setup we used in the previous article. However, we would like to amend it so that we can generate an unlimited number of specs. If you need an explanation on how to configure the OpenAPI Gradle plugin, please refer to my previous article.

The first step is to introduce a new class into our Gradle build script. This class will hold all the required information about the spec.

```kotlin
/**
 * A class representing a specific spec to generate.
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

Now, we will create a list of all the specs we want to generate in our `build.gradle.kts`:

```kotlin
/**
 * List of all API specs to generate.
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

**Note:** If you noticed, all of our specs are generated into the same output directory. This is because OpenAPI generates some infrastructure classes that are used by the generated code. If we do not generate them in the same directory, the `sourceDir` will include duplications of classes in the same packages, and the code will not compile.

You can see that we are still separating our APIs by package name in the generated code:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1670669752006/tuuB0fh8d.png align="center")

The next step is defining some generic functions that will:

* Register a new task for each spec to generate the code.
    
* Add the generated code to the source set.
    
* Ensure that the `clean` task is finalized by the generation task of the spec.
    
* Make `compileKotlin` depend on the generation task of the spec.
    

Let's get started.

We will start by creating the tasks for each of our specs under the `openapi tools` group:

```kotlin
// Iterate over the API list and register them as generator tasks
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
        api.templateDir?.let { this.templateDir.set(it) }
    }
}
```

The next step is adding our generated code from the previous tasks to our `sourceSet`. We are using the first element in the list since all of our files are generated into the same output directory.

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

**Note:** We are including the `ApiClient.kt`, `OAuth.kt`, and `OAuthOkHttpClient.kt` as there is currently a bug in the generator that prevents us from generating Retrofit clients with Jackson as the serialization library. If the bug is fixed, you can remove the `exclude` part from your project.

The last step is to ensure that the `clean` task and `compileKotlin` work well with the new tasks. We will create a new task called `cleanGeneratedCodeTask` that will delete all generated code whenever the `clean` task is run. Additionally, we want to ensure that all code generation tasks have been executed before building our code.

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

The last thing we need to do is add some dependencies to our `build.gradle.kts` to ensure that our code can be compiled.

```kotlin
dependencies {
    api("com.squareup.retrofit2:retrofit:$retrofitVersion")
    api("com.squareup.retrofit2:converter-jackson:$retrofitVersion")
    api("com.squareup.okhttp3:logging-interceptor:$okHttpVersion")
}
```

## Using Our Generated Code

### Domain Gateway Controller

The domain gateway controller implementation is very simple. It is a Spring `@RestController` that implements a given interface. Our code would look like this:

```kotlin
@RestController
class DomainGatewayController : GatewayApi {

    override fun hello(name: String): ResponseEntity<HelloResponse> = TODO()

    override fun goodbye(name: String): ResponseEntity<GoodbyeResponse> = TODO()
}
```

### Clients

As mentioned earlier, there is currently a bug in the OpenAPI generator that prevents us from using the auto-generated client. Therefore, we will have to define the Retrofit clients manually.

We will start by defining our `OkHttp` client. Note that I am setting the logger interceptor level to `BODY`. This is great for debugging, but **NEVER** use it in production as it will log all request and response bodies. This may expose sensitive information in your logs. If you still want to log your request/response body, you need to build a custom interceptor.

```kotlin
private fun okHttpClient() =
    OkHttpClient
        .Builder()
        .addInterceptor(HttpLoggingInterceptor().apply { level = BODY })
        .build()
```

The next step is to define the Jackson converter factory. The factory requires the `ObjectMapper` from the Spring context. Do not create a new `ObjectMapper` for it, as doing so may cause issues.

```kotlin
@Bean
fun jacksonConverterFactory(objectMapper: ObjectMapper): JacksonConverterFactory =
    JacksonConverterFactory.create(objectMapper)
```

Next, we will define beans for our two API clients. Note that while we are using the converter from above, we are injecting a generic `Factory`. This makes it easier to replace the library in the future.

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
        .baseUrl("http://goodbye-service:8789")
        .client(okHttpClient())
        .build()
        .create(GoodbyeApi::class.java)
```

That's it! From this point forward, we can use our clients as follows:

```kotlin
helloApi.hello("Yonatan")
goodbyeApi.goodbye("Yonatan")
```

## Conclusion

In this article, I have explained the benefits of aggregating your services into a domain gateway. We have used the power of OpenAPI to ensure the robustness of our API, both publicly (for our clients) and internally (between our services).

All code examples in this article are available in my GitHub repository: [https://github.com/yonatankarp/domain-gateway-demo](https://github.com/yonatankarp/domain-gateway-demo)

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More Information

* [API Versioning with Kotlin and Spring Boot](https://medium.com/towardsdev/api-versioning-with-kotlin-and-spring-boot-ef9e08214526) by Mariusz Sołtysiak: An article by a colleague of mine about API versioning used in their domain gateway.
    
* [Java Design Patterns - API Gateway](https://java-design-patterns.com/patterns/api-gateway/): An article on the API Gateway pattern from the Java Design Patterns website.
