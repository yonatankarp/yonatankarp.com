# How to write robust REST API with OpenAPI

While working as a backend engineer, I developed a few REST APIs. One thing that always happened to me (no matter how simple or complicated the API was) is that the integration between the backend and the clients has never been smooth. A typo in the URL, using camel case field name in the JSON instead of snake case, passing value as a string instead of an integer, and many more examples have happened to us.

![Frustration](https://cdn.hashnode.com/res/hashnode/image/upload/v1639499982620/rh2TfEC53.png align="left")

2 years ago, my team and I had to design a new API that will be integrated by multiple clients: mobile (Android & iPhone), web, and other backend services. We wanted to make the integration as painless as possible, and the API definition as robust as possible. During our brainstorming about the design of the API, we decided to go with OpenAPI (previously known as Swagger). I knew Swagger before, but only as a documentation tool for already written code. This time, we decided to build the API the other way around. We started by defining the spec with all the endpoints, requests & responses, and then, each integrator of the spec (backend, mobile, and web) has auto-generated the models, clients, or controllers and used them in the codebase, so there was no room for mistakes.

The design was a big success, and we've replicated the same behavior again and again for new APIs to come. More and more teams started to adopt OpenAPI for this exact purpose. Actually, by now, it became such a big success that the company decided to adopt OpenAPI as the official way to describe all of our Apis for integration inside and outside the company.

In this article, I will show a short and simple API example and the process we did to have a working REST API. In our tech stack, we will use SpringBoot as the framework, Kotlin as the language, and Gradle's Kotlin DSL as the build system - but please note, OpenAPI supports many different languages and I just decided on this one. For the full list, you can check [this](https://openapi-generator.tech/docs/generators) link

# Step 1 - Design the API

![OpenAPI Editor](https://cdn.hashnode.com/res/hashnode/image/upload/v1639565684934/q9uR7rz6k.png align="left")

We want to design a simple API. Our API will get a name on the endpoint `/greet` and a name to greet as the query parameter (e.g. `/greet&name=Yonatan`). We will respond with the answer `hello + $name`.

Let's start by defining our spec! I would highly encourage you to use the [Swagger Editor](https://editor.swagger.io/) to detect syntax errors in your spec in advance. If you're using IntelliJ IDEA, you can also use the [OpenAPI Editor plugin](https://plugins.jetbrains.com/plugin/14837-openapi-swagger-editor), which works extremely well.

We will use this definition:

```yml
openapi: 3.0.3

info:
  title: Greeting API
  description: "An API that will send you a greet according to a given name"
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
      description: Greeting a given name.
      tags:
        - Greeting

      parameters:
        - in: query
          name: name
          schema:
            type: string
          required: false
          description: The name to greet if no name supplied the API will greet the world

      responses:
        200:
          description: Returning a greeting with the given name.
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
          description: The greet from the API.
          example: "Hello, Yonatan!"

      required:
        - greet
```

Let's look at the spec step by step and understand each section.

### API Information

In the `info` section, we define the information that will appear on our documentation, alongside the API version, the owners of the API, etc. You can find more attributes to add to this section at [API General Info](https://swagger.io/docs/specification/api-general-info/).

### Servers

This list of servers will allow the people who use the documentation made from the spec to select the environment they want to use (e.g. local, dev, staging, and production). For example:

```yml
servers:
	 - url: https://dev.env
	 description: The development environment.
	 - url: https://staging.env
	 description: The staging environment.
	 - url: https://production.env
	 description: The production environment.
```

### The endpoints

Under the `path` section, we will define all the different endpoints that would be part of our API, their HTTP methods, request & responses.

### The models

In the `components` section, are defining the different models that would be part of our API. In our case, we simply define a response for the endpoint that will contain a single field (that cannot be `null` as it's marked as `required`). This section can contain more information such as the security schema (e.g. which authentication method are we using in our API, general headers that are required, and more)

# Step 2 - Generating the models

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1639567865423/3innuBle4A.png align="left")

To Generate our models, we will use [OpenAPI Generator](https://openapi-generator.tech/), more specifically, we will use [OpenAPI Generator Gradle plugin](https://openapi-generator.tech/docs/plugins/#gradle).

First, let's store our spec into our project, we can create an `api` directory at the project root, and store it into a `spec.yml` file. Your project should look like this:

![OpenAPI Spec file tree](https://cdn.hashnode.com/res/hashnode/image/upload/v1639568679477/Cel1PdiU_n.png align="left")

The next step would be to add a `config.json` file to our `api` directory that will include the different flags that we would like to set. Let's create that file with the following content:

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

You can find all the different available flags that you can set here in the generator [documentation](https://openapi-generator.tech/docs/generators/kotlin-spring).

Your project should look now like this:

![project structure with config.json](https://cdn.hashnode.com/res/hashnode/image/upload/v1639569210237/UbmCjphC6.png align="left")

Now, let's start by adding the plugin to our project. Open your `build.gradle.kt` and add the following to your plugins:

```kotlin
plugins {
    id("org.openapi.generator") version "5.3.0"
}
```

Now, finally, let's configure the Gradle plugin to work with all the things we've set! We will build our models into the `build` directory on each build of our project, so we will not need to commit any auto-generated code to our project.

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

Note, it is possible to override the templates coming from the generator in case you need to add/remove something from the generated classes. To do so, copy the templates you need from the [generator repository](https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator/src/main/resources), amend them, and set the files in the plugin with the following line:

```kotlin
openApiGenerate {
    templateDir.set(apiDirectoryPath + File.separator + "templates")
}
```

For more information about the plugin configurations click [here](https://github.com/OpenAPITools/openapi-generator/blob/master/modules/openapi-generator-gradle-plugin/README.adoc)

I would recommend adding the following code that will make sure that every time we're using the `clean` command our generated code would be cleaned as well, and that each `build` will be deepened on the generated code to exist.

```kotlin
tasks {
    register("cleanGeneratedCodeTask") {
        description = "Removes generated Open API code"

        doLast {
            File(generatedCodeDirectoryPath).deleteRecursively()
        }
    }

    clean { dependsOn("cleanGeneratedCodeTask"); finalizedBy(openApiGenerate) }
    compileJava { dependsOn(openApiGenerate) }
}
```

Last but not least, let's make sure that all of our code is included in our source set:

```kotlin
sourceSets[SourceSet.MAIN_SOURCE_SET_NAME].java {
    srcDir(generatedCodeDirectoryPath + File.separator +
         "src" + File.separator + "main" + File.separator + "kotlin")
}
```

If you'll run the `build` command now in Gradle you should see something similar in your project files:

![build directory generated code](https://cdn.hashnode.com/res/hashnode/image/upload/v1639571564095/8PSYIyADv.png align="left")

As you can see, the code currently has errors as it cannot find the `javax.validation` package. we can easily solve it by adding to our `build.gradle.kt` the following dependency:

```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-validation")
}
```

# Step 3 - Implement the API! 🎉

![IMG_20190304_180316__01_compressed.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1639578297526/9Bp_e3Ma_.jpeg align="left")

This stage is the easiest step so far. We will implement our generated interface `GreetingApi` in our controller. Let's write the code!

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

We can now run our server and try to call it from the browser:

![Greeting from our server](https://cdn.hashnode.com/res/hashnode/image/upload/v1639574118597/enO5W6Q8J.png align="left")

# Step 4 - Writing tests

Let's add a few tests to our API! Since we have no actual business logic here, I would have only an integration test to our new code that will check the flow completely.

Lets start by defining our `WebConfig` class in our `src/main/kotlin` directory with the following content:

```kotlin
@Configuration
@ComponentScan(
    basePackageClasses = [GreetingApplication::class],
    includeFilters = [ComponentScan.Filter(RestController::class)]
)
class WebConfig
```

Now, we can add our test class. I would use the `@ParameterizedTest` functionality of jUnit5 to avoid code duplications. Let's look at the code:

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
        // Given uri and a request
        val uri = if (testCase.name.isNullOrBlank()) "/v1/greet"
        else "/v1/greet?name=${testCase.name}"

        val request = MockMvcRequestBuilders.get(uri)
            .accept(MediaType.APPLICATION_JSON)

        // When we call the api
        val response =
            mockMvc
                .perform(request)
                .andExpect(MockMvcResultMatchers.status().isOk)
                .andReturn()
                .response
                .contentAsString

        // Then we expect to have a response with the correct greeting
        val actualGreeting = ObjectMapper()
            .readTree(response)["greet"]
            .asText()
        assertEquals(testCase.expectedResult, actualGreeting)
    }
}
```

If you did everything correctly, by running the test you should see that you have 2 green tests! 🎉

![Unit test passing](https://cdn.hashnode.com/res/hashnode/image/upload/v1639577839159/lyOFaH_Bm.png align="left")

The finished project is also available on my GitHub repository so you can see the finished code there. you can visit by clicking [here](https://github.com/yonatankarp/openapi-usage-example).