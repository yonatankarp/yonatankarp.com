---
title: "Observability in Action Part 1: Enhancing Your Codebase with OpenTelemetry"
subtitle: "A Step-by-Step Guide to Building a Kotlin Client Library for Cat Facts API"
slug: observability-in-action-part-1-enhancing-your-codebase-with-opentelemetry
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: kotlin, observability, opentelemetry, devops, springboot
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/oQbTpK4rXr0/upload/f7dc126791503f3947688e6810e40231.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin and OpenTelemetry: A Guide to Better Code Observability"
seoDescription: "Boost Kotlin code with OpenTelemetry for observability. Learn to build a client library and effectively monitor your service performance."
seriesSlug: spring-boot
# Table of contents
enableToc: true
saveAsDraft: true
---

> **TL;DR:** In this article, you'll learn how to build a client library for fetching cat facts from an API using Kotlin. The library can handle multiple concurrent API calls and return unique facts. OpenTelemetry and service instrumentation will be covered in later parts of this series to enhance observability.

## Introduction

In this series, we'll delve into the steps for adding observability to your codebase. Initially, we'll develop a library that retrieves data from a remote API. Following that, we'll construct a service using this library to fetch and save this data in a database.

As we progress, we'll infuse observability into the service, demonstrating their behavior in an environment resembling production. Once the service is equipped with instrumentation, we'll introduce a filter to it that dismisses overly large requests. This filter, too, will be instrumented.

To wrap up, we'll integrate an OpenTelemetry collector into our service. This will gather all traces and metrics, transmitting them to an external location to mitigate any service overhead.

### Series Outline

* Part 1 - Build Client Library ⬅ You are here

* Part 2 - Build the Service

* Part 3 - Instrument the Service

* Part 4 - Instrument a Spring Filter

* Part 5 - Integrate the OpenTelemetry Collector


All code examples for this series are available on GitHub:

* [cat-fact-client](https://www.github.com/yonatankarp/cat-fact-client)

* [cat-fact-service](https://www.github.com/yonatankarp/cat-fact-service)


![kitten poking cat's nose](https://cdn.hashnode.com/res/hashnode/image/upload/v1693143753996/8ouuXARBw.avif?auto=format align="left")

## Introduction to Service Instrumentation

What is service instrumentation? And why do I need it?

Service instrumentation is the process of collecting data from different components in your system (e.g. services) to benefit insights into the system's performance, behavior, and usage. This data can be used to optimize the system, troubleshoot, and improve the user experience.

More specifically, we will use [OpenTelemetry](https://opentelemetry.io/). OpenTelemetry documentation states:

> OpenTelemetry, also known as OTel for short, is a vendor-neutral open-source Observability framework for instrumenting, generating, collecting, and exporting telemetry data such as traces, metrics, logs. As an industry-standard, it is natively supported by a number of vendors.

That means that OpenTelemetry is a framework that allows you to easily add instrumentation to your codebase and collect the data in a vendor-agnostic way. It supports multiple programming languages and provides a unified API for collecting and exporting telemetry data to various backends.

OpenTelemetry also provides a set of libraries and integrations that make it easy to instrument popular frameworks, libraries, and services. With OpenTelemetry, developers can easily add telemetry to their services and gain visibility into their systems' performance and behavior.

For more information about observability, check out a great article ["How Observability Changed My (Developer) Life"](https://medium.com/better-programming/how-observability-changed-my-developer-life-453b9807de7a) written by a colleague of mine, [Mariusz Sołtysiak](https://medium.com/@mariuszsoltysiak).

![Cat in helmet](https://cdn.hashnode.com/res/hashnode/image/upload/v1693143830112/YGFMlHRZ_.jpeg?auto=format align="center")

## Building the Client Library

We'll kick off by developing our client library named `cat-fact-client`. This library will fetch cat facts from the [Cat Facts API](https://catfact.ninja/fact).

At its core, our library is straightforward. It endeavors to fetch a specified number of facts. While the API restricts fact selection, we compensate by invoking the API multiple times, as required, making the best effort to serve the requested number of facts.

Our library will utilize:

* [Kotlin](https://kotlinlang.org/) - The crux of our library, it will be scripted in Kotlin using coroutines.

* [Gradle](https://gradle.org/) - Our trusted build system and dependency manager.

* [Retrofit](https://square.github.io/retrofit/) - Our choice for an HTTP client.

* [Jackson](https://github.com/FasterXML/jackson) - Essential for serialization, particularly as we’ll be integrating with [Spring Boot](https://spring.io/projects/spring-boot) which defaults to Jackson.


Let’s get coding!

![Cat writing code](https://cdn.hashnode.com/res/hashnode/image/upload/v1692865359929/5ac1a5a9-15c0-4e29-bddc-5f97879ac8e2.gif align="center")

### Adding Dependencies

Kick-off by adding the essential dependencies to the `build.gradle.kts` file:

```kotlin
dependencies {
    // Coroutines
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3")

    // Serialization
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin:2.15.2")

    // Retrofit
    api("com.squareup.okhttp3:okhttp:4.11.0")
    api("com.squareup.retrofit2:retrofit:2.9.0")
    api("com.squareup.retrofit2:converter-jackson:2.9.0")
}
```

### Domain Modeling

When you ping the [Cat Facts API](https://catfact.ninja/fact), expect a response similar to:

```json
{"fact":"Cats have \"nine lives\" thanks to a flexible spine and powerful leg and back muscles","length":83}
```

Our primary concern is the `fact` field. To determine the fact length, we simply utilize `fact.length`. This gives rise to our model:

```kotlin
data class Fact(val value: String)
```

By leveraging Kotlin's [value class](https://kotlinlang.org/docs/inline-classes.html), we optimize resource utilization. While we interact solely with Fact objects, these objects are substituted with String objects during compilation.

Thus, we revised our code to:

```kotlin
@JvmInline
value class Fact(val value: String)
```

### Constructing the HTTP Client

Having established our domain model, it's time to construct an HTTP client for API calls.

This would be our client's blueprint:

```kotlin
import com.fasterxml.jackson.annotation.JsonIgnoreProperties
import retrofit2.http.GET

internal interface CatFactClient {
    @GET("fact")
    suspend fun fact(): CatFactResponse
}

@JsonIgnoreProperties(ignoreUnknown = true)
internal data class CatFactResponse(
    val fact: String,
)
```

You’ll observe a solitary function, `fact()`, geared towards API communication, yielding a `CatFactResponse`. We’ve intentionally omitted the `length` field, as highlighted earlier.

### Connecting everything together

With foundational pieces in place, let's merge them to manifest our core library logic.

Commence by configuring an instance of the HTTP client:

```kotlin
private const val API_BASE_URL = "https://catfact.ninja/"

private var client = Retrofit.Builder()
    .baseUrl(API_BASE_URL)
    .client(OkHttpClient.Builder().build())
    .addConverterFactory(JacksonConverterFactory.create(objectMapper))
    .build()
    .create<CatFactClient>()
```

Now, our business logic:

```kotlin
override suspend fun get(numberOfFacts: Int): Set<Fact> =
    coroutineScope {
        (1..numberOfFacts).map {
            async { client.fact() }
        }.awaitAll()
            .map { Fact(it.fact) }
            .toSet()
    }
```

This function concurrently dispatches `numberOfFacts` calls to the API, awaits all replies, translates them into the domain model, and returns a fact set. We utilize `Set` over `List` since the API doesn't assure unique responses.

Inspect the finalized version of the code [here](https://github.com/yonatankarp/cat-fact-client/blob/implement-fetch-cat-facts/src/main/kotlin/cat/fact/client/CatFactProvider.kt).

![Happy Cat](https://cdn.hashnode.com/res/hashnode/image/upload/v1693143872825/kKuvJTSlm.webp?auto=format align="center")

This piece isn’t tailored to guide library publishing. However, if you’re inclined, relevant settings can be found [here](https://github.com/yonatankarp/cat-fact-client/blob/implement-fetch-cat-facts/build.gradle.kts).

Our library's artifact, version `0.1.0`, is available on GitHub packages and awaits your exploration. An updated version (`0.2.0`) offers mock implementations, bypassing internet prerequisites with a few breaking changes. Nevertheless, the core remains unaltered.

## Conclusion

This article provides a comprehensive guide on building a client library for fetching cat facts from an API, using Kotlin, Gradle, Retrofit, and Jackson. The library is designed to handle multiple concurrent API calls and return a set of unique facts. The implementation of OpenTelemetry and service instrumentation is planned for the next parts of this series, ultimately enhancing the observability of the service.

%%[contact-me]

## Acknowledgments

* [Mariusz Sołtysiak](https://medium.com/@mariuszsoltysiak) - for moral support, review, and suggestions while writing this series of articles.
