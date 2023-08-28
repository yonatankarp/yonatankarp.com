---
title: "How to Write a Robust REST API with OpenAPI"
subtitle: "Building REST API with OpenAPI, SpringBoot and Kotlin"
slug: how-to-write-a-robust-rest-api-with-openapi
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: gradle, guide, kotlin, springboot, openapi
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/qWwpHwip31M/upload/v1639559550032/L-pLzXJ3Q.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "A Guide to Writing Robust REST APIs with OpenAPI | Learn How to Design"
seoDescription: "Learn how to design & implement robust REST APIs using OpenAPI. Achieve seamless integration & follow best practices for backend-client communication."
seriesSlug: spring-boot
# Table of contents
enableToc: true
saveAsDraft: false
---

As a backend engineer, I have developed numerous REST APIs, and one recurring issue I faced was the lack of smooth integration between the backend and clients. Typos in URLs, inconsistent casing in JSON (camel case vs. snake case), passing values of the wrong type (e.g., string instead of an integer), and other similar mistakes have happened to us multiple times.

![Frustration](https://cdn.hashnode.com/res/hashnode/image/upload/v1639499982620/rh2TfEC53.png align="left")

Two years ago, my team and I had to design a new API that would integrate with multiple clients: mobile (Android & iPhone), web, and other backend services. We aimed to make the integration process as seamless as possible while ensuring the robustness of the API definition. During our brainstorming sessions, we decided to use OpenAPI (formerly known as Swagger) from the beginning. Although I was already familiar with Swagger as a documentation tool, this time we chose to build the API in reverse. We started by defining the API specification with all the endpoints, requests, and responses. Each team responsible for integration (backend, mobile, and web) would then auto-generate the code they needed, including models, clients, and controllers. By using these generated code components in our codebase, we eliminated room for errors.

This design approach proved to be a great success. We replicated the same process for new APIs and witnessed other teams adopting OpenAPI for their APIs as well. It became so popular within the company that OpenAPI was officially adopted as the standard for describing all our APIs. This made it easier for both internal and external integrators to adopt our APIs.

In this article, I will demonstrate an example of a short and simple API and walk you through the process we followed to create a working REST API. For this demonstration, we will use the following technology stack: Spring Boot, Kotlin, and Gradle's Kotlin DSL. However, OpenAPI supports various other languages, and I chose this stack for the sake of this example. You can refer to [this link](https://openapi-generator.tech/docs/generators) for a full list of supported languages.

## Step 1: Design the API

![OpenAPI Editor](https://cdn.hashnode.com/res/hashnode/image/upload/v1639565684934/q9uR7rz6k.png align="left")

Let's begin by designing a simple API. Our API will have an endpoint `/greet` that accepts a name as a query parameter (e.g., `/greet?name=Yonatan`). The API will respond with the greeting "hello" followed by the provided name.

To define our API, we can use the Swagger Editor to detect any syntax errors in advance. If you are using IntelliJ IDEA, you can also utilize the OpenAPI Editor plugin.

Here's our API definition in YAML format:

```yml
openapi: 3.0.3

info:
  title: Greeting API
  description: "An API that sends a greeting based on a provided name"
  contact:
    name: Yonatan Karp-Rudin
    url: https://yonatankarp.com
  version: 0.1.0

tags:
  - name: Greeting

servers:
  - url: http://localhost:8080/v1
    description: Local development environment

paths:
  /greet:
    get:
      operationId: greet_name
      description: Greet a given name.
      tags:
        - Greeting

      parameters:
        - in: query
          name: name
          schema:
            type: string
          required: false
          description: The name to greet. If no name is provided, the API will greet the world.

      responses:
        200:
          description: Returns a greeting with the provided name.
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/GreetResponse"

components:
  schemas:
    GreetResponse:
      type: object
      properties:
        greet:
          type: string
          description: The greeting from the API.
          example: "Hello, Yonatan!"

      required:
        - greet
```

Let's go through the specification step by step and understand each section.

### API Information

In the `info` section, we provide details about the API that will appear in the documentation. This includes the API's title, description, and contact information. You can find additional attributes to include in this section in the [API General Info documentation](https://swagger.io/docs/specification/api-general-info/).

### Servers

The `servers` section lists the available environments (e.g., dev, staging, production) for users to select when referring to the API documentation.

```yml
servers:
	 - url: https://dev.env
	 description: The development environment.
	 - url: https://staging.env
	 description: The staging environment.
	 - url: https://production.env
	 description: The production environment.
```

### Endpoints

The `paths` section defines the different endpoints of our API, including their HTTP methods, request parameters, and responses.

### Models

In the `components` section, we define the API models. In this case, we define a response model for an endpoint with a single field. Note that this field is marked as required and cannot be null. The `components` section can include additional information, such as security schemas (authentication method, required headers, etc.).

## Step 2: Generating the Models

![OpenAPI Generator](https://cdn.hashnode.com/res/hashnode/image/upload/v1639567865423/3innuBle4A.png align="left")

To generate the models, we will use the OpenAPI Generator, specifically the OpenAPI Generator Gradle plugin.

First, let's store the API specification in our project. Create an `api` directory at the project root and save the specification in a `spec.yml` file. Your project structure should resemble this:

![OpenAPI Spec file tree](https://cdn.hashnode.com/res/hashnode/image/upload/v1639568679477/Cel1PdiU_n.png align="left")

Next, create a `config.json` file inside the `api` directory to define the desired configuration flags. Here's an example content for the `config.json` file:

```json
{
  "interfaceOnly": true,
  "modelPackage": "com.yonatankarp.openapi.models",
  "apiPackage": "com.yonatankarp.openapi",
  "implicitHeaders": true,
  "hideGenerationTimestamp": true,
  "useTags": true
}
```

You can find the available generator flags in the [documentation](https://openapi-generator.tech/docs/generators/kotlin-spring).

Your project structure should now resemble this:

![Project structure with config.json](https://cdn.hashnode.com/res/hashnode/image/upload/v1639569210237/UbmCjphC6.png align="left")

Now, let's add the OpenAPI Generator plugin to our project. Open your `build.gradle.kt` file and include the following plugin:

```kotlin
plugins {
    id("org.openapi.generator") version "5.3.0"
}
```

Next, configure the Gradle plugin to work with the settings we defined. We will generate our models into the `build` directory, ensuring that the auto-generated code is not committed to our project repository.

```kotlin
val apiDirectoryPath = projectDir.absolutePath + File.separator + "api"
val generatedCodeDirectoryPath = buildDir.path + File.separator +
       "generated" + File.separator + "open-api"

openApiGenerate {
    generatorName.set("kotlin-spring")
    inputSpec.set(apiDirectoryPath + File.separator + "spec.yml")
    outputDir.set(generatedCodeDirectoryPath)
    configFile.set(apiDirectoryPath + File.separator + "config.json")
}
```

Note that you can override the generator's default templates if needed. You can copy the templates from the [generator repository](https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator/src/main/resources), modify them, and set them in the plugin using the `templateDir` property.

```kotlin
openApiGenerate {
    templateDir.set(apiDirectoryPath + File.separator + "templates")
}
```

For more information about the plugin's configurations click [here](https://github.com/OpenAPITools/openapi-generator/blob/master/modules/openapi-generator-gradle-plugin/README.adoc)

It's also recommended to add the following code to ensure that running the `clean` command removes the generated code as well. Additionally, make the `build` command depend on the OpenAPI generation task to generate the latest code with each build.

```kotlin
tasks {
    register("cleanGeneratedCodeTask") {
        description = "Removes generated OpenAPI code"

        doLast {
            File(generatedCodeDirectoryPath).deleteRecursively()
        }
    }

    clean { dependsOn("cleanGeneratedCodeTask"); finalizedBy(openApiGenerate) }
    compileJava { dependsOn(openApiGenerate) }
}
```

Lastly, ensure that the source set includes the generated code:

```kotlin
sourceSets[SourceSet.MAIN_SOURCE_SET_NAME].java {
    srcDir(generatedCodeDirectoryPath + File.separator +
         "src" + File.separator + "main" + File.separator + "kotlin")
}
```

Running the `build` command in Gradle should generate the code in your project files, similar to the following:

![Build directory with generated code](https://cdn.hashnode.com/res/hashnode/image/upload/v1639571564095/8PSYIyADv.png align="left")

As you can see, the code currently shows errors due to the missing `javax.validation` package. To resolve this, add the following dependency to your `build.gradle.kt` file:

```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-validation")
}
```

## Step 3: Implement the API! 🎉

![API Implementation](https://cdn.hashnode.com/res/hashnode/image/upload/v1639578297526/9Bp_e3Ma_.jpeg align="left")

This step is the easiest so far. We will implement our generated interface, `GreetingApi`, in our controller. Let's write the code!

```kotlin
@RestController
class GreetingApiController : GreetingApi {

    override fun greetName(
        @RequestParam(value = "name", required = false) name: String?
    ): ResponseEntity<GreetResponse> =
        if (name.isNullOrBlank())
            ResponseEntity.ok(GreetResponse("Hello, world!"))
        else
            ResponseEntity.ok(GreetResponse("Hello, $name!"))
}
```

We can now run our server and test it by making a request in the browser:

![Greeting from our server](https://cdn.hashnode.com/res/hashnode/image/upload/v1639574118597/enO5W6Q8J.png align="left")

## Step 4: Writing Tests

Let's add some tests for our API. Since there's no business logic in this example, we will only include an integration test to cover the complete flow.

First, let's define the `WebConfig` class in the `src/main/kotlin` directory:

```kotlin
@Configuration
@ComponentScan(
    basePackageClasses = [GreetingApplication::class],
    includeFilters = [ComponentScan.Filter(RestController::class)]
)
class WebConfig
```

Now, we can add our test class. We will utilize jUnit5's `@ParameterizedTest` functionality to avoid code duplication. Here's the test code:

```kotlin
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@ExtendWith(SpringExtension::class)
@ContextConfiguration(classes = [WebConfig::class])
@AutoConfigureMockMvc
@WebAppConfiguration
class GreetingApiControllerTest(context: WebApplicationContext) {
    private val mockMvc = MockMvcBuilders
        .webAppContextSetup(context).build()

    data class TestCase(val name: String?, val expectedResult: String)

    private fun getTestCase(): List<Arguments> =
        arrayOf(
            TestCase(name = "test", expectedResult = "Hello, test!"),
            TestCase(name = null, expectedResult = "Hello, world!"),
            TestCase(name = "💩", expectedResult = "Hello, 💩!"),
        ).map { Arguments.of(it) }

    @ParameterizedTest
    @MethodSource("getTestCase")
    fun `should return correct greeting`(testCase: TestCase) {
        // Given the URI and a request
        val uri = if (testCase.name.isNullOrBlank()) "/v1/greet"
        else "/v1/greet?name=${testCase.name}"

        val request = MockMvcRequestBuilders.get(uri)
            .accept(MediaType.APPLICATION_JSON)

        // When we call the API
        val response =
            mockMvc
                .perform(request)
                .andExpect(MockMvcResultMatchers.status().isOk)
                .andReturn()
                .response
                .contentAsString

        // Then we expect a response with the correct greeting
        val actualGreeting = ObjectMapper()
            .readTree(response)["greet"]
            .asText()
        assertEquals(testCase.expectedResult, actualGreeting)
    }
}
```

If you have followed the instructions correctly, running the test should result in two passing tests! 🎉

![Unit test passing](https://cdn.hashnode.com/res/hashnode/image/upload/v1639577839159/lyOFaH_Bm.png align="left")

You can find the complete code for this project on my GitHub repository. Feel free to explore it [here](https://github.com/yonatankarp/openapi-usage-example).

## Conclusion

Using OpenAPI enables us to ensure smooth, robust, and efficient integration processes. Whenever you design a new API, consider using an API schema instead of writing it manually.

%%[contact-me]
