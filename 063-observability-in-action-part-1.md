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
saveAsDraft: false
---

This blog post was moved to [This blog post was moved to This blog post was moved to https://kotlinbackend.com/observability-in-action-how-to-use-opentelemetry/.

[//]: # (> **TL;DR:** In this article, you'll learn how to build a client library for fetching cat facts from an API using Kotlin. The library can handle multiple concurrent API calls and return unique facts. OpenTelemetry and service instrumentation will be covered in later parts of this series to enhance observability.)

[//]: # ()
[//]: # (## Introduction)

[//]: # ()
[//]: # (In this series, we'll delve into the steps for adding observability to your codebase. Initially, we'll develop a library that retrieves data from a remote API. Following that, we'll construct a service using this library to fetch and save this data in a database.)

[//]: # ()
[//]: # (As we progress, we'll infuse observability into the service, demonstrating their behavior in an environment resembling production. Once the service is equipped with instrumentation, we'll introduce a filter to it that dismisses overly large requests. This filter, too, will be instrumented.)

[//]: # ()
[//]: # (To wrap up, we'll integrate an OpenTelemetry collector into our service. This will gather all traces and metrics, transmitting them to an external location to mitigate any service overhead.)

[//]: # ()
[//]: # (### Series Outline)

[//]: # ()
[//]: # (* [Part 1 - Build Client Library]&#40;https://yonatankarp.com/observability-in-action-part-1-enhancing-your-codebase-with-opentelemetry&#41; ⬅ You are here)

[//]: # ()
[//]: # (* [Part 2 - Build the Service]&#40;https://yonatankarp.com/observability-in-action-part-2-enhancing-your-codebase-with-opentelemetry&#41;)

[//]: # ()
[//]: # (* [Part 3 - Instrument the Service]&#40;https://yonatankarp.com/observability-in-action-part-3-enhancing-your-codebase-with-opentelemetry&#41;)

[//]: # ()
[//]: # (* Part 4 - Integrate the OpenTelemetry Collector)

[//]: # ()
[//]: # ()
[//]: # (All code examples for this series are available on GitHub:)

[//]: # ()
[//]: # (* [cat-fact-client]&#40;https://www.github.com/yonatankarp/cat-fact-client&#41;)

[//]: # ()
[//]: # (* [cat-fact-service]&#40;https://www.github.com/yonatankarp/cat-fact-service&#41;)

[//]: # ()
[//]: # ()
[//]: # (![kitten poking cat's nose]&#40;https://cdn.hashnode.com/res/hashnode/image/upload/v1693143753996/8ouuXARBw.avif?auto=format align="left"&#41;)

[//]: # ()
[//]: # (## Introduction to Service Instrumentation)

[//]: # ()
[//]: # (What is service instrumentation? And why do I need it?)

[//]: # ()
[//]: # (Service instrumentation is the process of collecting data from different components in your system &#40;e.g. services&#41; to benefit insights into the system's performance, behavior, and usage. This data can be used to optimize the system, troubleshoot, and improve the user experience.)

[//]: # ()
[//]: # (More specifically, we will use [OpenTelemetry]&#40;https://opentelemetry.io/&#41;. OpenTelemetry documentation states:)

[//]: # ()
[//]: # (> OpenTelemetry, also known as OTel for short, is a vendor-neutral open-source Observability framework for instrumenting, generating, collecting, and exporting telemetry data such as traces, metrics, logs. As an industry-standard, it is natively supported by a number of vendors.)

[//]: # ()
[//]: # (That means that OpenTelemetry is a framework that allows you to easily add instrumentation to your codebase and collect the data in a vendor-agnostic way. It supports multiple programming languages and provides a unified API for collecting and exporting telemetry data to various backends.)

[//]: # ()
[//]: # (OpenTelemetry also provides a set of libraries and integrations that make it easy to instrument popular frameworks, libraries, and services. With OpenTelemetry, developers can easily add telemetry to their services and gain visibility into their systems' performance and behavior.)

[//]: # ()
[//]: # (For more information about observability, check out a great article ["How Observability Changed My &#40;Developer&#41; Life"]&#40;https://medium.com/better-programming/how-observability-changed-my-developer-life-453b9807de7a&#41; written by a colleague of mine, [Mariusz Sołtysiak]&#40;https://medium.com/@mariuszsoltysiak&#41;.)

[//]: # ()
[//]: # (![Cat in helmet]&#40;https://cdn.hashnode.com/res/hashnode/image/upload/v1693143830112/YGFMlHRZ_.jpeg?auto=format align="center"&#41;)

[//]: # ()
[//]: # (## Building the Client Library)

[//]: # ()
[//]: # (We'll kick off by developing our client library named `cat-fact-client`. This library will fetch cat facts from the [Cat Facts API]&#40;https://catfact.ninja/fact&#41;.)

[//]: # ()
[//]: # (At its core, our library is straightforward. It endeavors to fetch a specified number of facts. While the API restricts fact selection, we compensate by invoking the API multiple times, as required, making the best effort to serve the requested number of facts.)

[//]: # ()
[//]: # (Our library will utilize:)

[//]: # ()
[//]: # (* [Kotlin]&#40;https://kotlinlang.org/&#41; - The crux of our library, it will be scripted in Kotlin using coroutines.)

[//]: # ()
[//]: # (* [Gradle]&#40;https://gradle.org/&#41; - Our trusted build system and dependency manager.)

[//]: # ()
[//]: # (* [Retrofit]&#40;https://square.github.io/retrofit/&#41; - Our choice for an HTTP client.)

[//]: # ()
[//]: # (* [Jackson]&#40;https://github.com/FasterXML/jackson&#41; - Essential for serialization, particularly as we’ll be integrating with [Spring Boot]&#40;https://spring.io/projects/spring-boot&#41; which defaults to Jackson.)

[//]: # ()
[//]: # ()
[//]: # (Let’s get coding!)

[//]: # ()
[//]: # (![Cat writing code]&#40;https://cdn.hashnode.com/res/hashnode/image/upload/v1692865359929/5ac1a5a9-15c0-4e29-bddc-5f97879ac8e2.gif align="center"&#41;)

[//]: # ()
[//]: # (### Adding Dependencies)

[//]: # ()
[//]: # (Kick-off by adding the essential dependencies to the `build.gradle.kts` file:)

[//]: # ()
[//]: # (```kotlin)

[//]: # (dependencies {)

[//]: # (    // Coroutines)

[//]: # (    implementation&#40;"org.jetbrains.kotlinx:kotlinx-coroutines-core:1.7.3"&#41;)

[//]: # ()
[//]: # (    // Serialization)

[//]: # (    implementation&#40;"com.fasterxml.jackson.module:jackson-module-kotlin:2.15.2"&#41;)

[//]: # ()
[//]: # (    // Retrofit)

[//]: # (    api&#40;"com.squareup.okhttp3:okhttp:4.11.0"&#41;)

[//]: # (    api&#40;"com.squareup.retrofit2:retrofit:2.9.0"&#41;)

[//]: # (    api&#40;"com.squareup.retrofit2:converter-jackson:2.9.0"&#41;)

[//]: # (})

[//]: # (```)

[//]: # ()
[//]: # (### Domain Modeling)

[//]: # ()
[//]: # (When you ping the [Cat Facts API]&#40;https://catfact.ninja/fact&#41;, expect a response similar to:)

[//]: # ()
[//]: # (```json)

[//]: # ({"fact":"Cats have \"nine lives\" thanks to a flexible spine and powerful leg and back muscles","length":83})

[//]: # (```)

[//]: # ()
[//]: # (Our primary concern is the `fact` field. To determine the fact length, we simply utilize `fact.length`. This gives rise to our model:)

[//]: # ()
[//]: # (```kotlin)

[//]: # (data class Fact&#40;val value: String&#41;)

[//]: # (```)

[//]: # ()
[//]: # (By leveraging Kotlin's [value class]&#40;https://kotlinlang.org/docs/inline-classes.html&#41;, we optimize resource utilization. While we interact solely with Fact objects, these objects are substituted with String objects during compilation.)

[//]: # ()
[//]: # (Thus, we revised our code to:)

[//]: # ()
[//]: # (```kotlin)

[//]: # (@JvmInline)

[//]: # (value class Fact&#40;val value: String&#41;)

[//]: # (```)

[//]: # ()
[//]: # (### Constructing the HTTP Client)

[//]: # ()
[//]: # (Having established our domain model, it's time to construct an HTTP client for API calls.)

[//]: # ()
[//]: # (This would be our client's blueprint:)

[//]: # ()
[//]: # (```kotlin)

[//]: # (import com.fasterxml.jackson.annotation.JsonIgnoreProperties)

[//]: # (import retrofit2.http.GET)

[//]: # ()
[//]: # (internal interface CatFactClient {)

[//]: # (    @GET&#40;"fact"&#41;)

[//]: # (    suspend fun fact&#40;&#41;: CatFactResponse)

[//]: # (})

[//]: # ()
[//]: # (@JsonIgnoreProperties&#40;ignoreUnknown = true&#41;)

[//]: # (internal data class CatFactResponse&#40;)

[//]: # (    val fact: String,)

[//]: # (&#41;)

[//]: # (```)

[//]: # ()
[//]: # (You’ll observe a solitary function, `fact&#40;&#41;`, geared towards API communication, yielding a `CatFactResponse`. We’ve intentionally omitted the `length` field, as highlighted earlier.)

[//]: # ()
[//]: # (### Connecting everything together)

[//]: # ()
[//]: # (With foundational pieces in place, let's merge them to manifest our core library logic.)

[//]: # ()
[//]: # (Commence by configuring an instance of the HTTP client:)

[//]: # ()
[//]: # (```kotlin)

[//]: # (private const val API_BASE_URL = "https://catfact.ninja/")

[//]: # ()
[//]: # (private var client = Retrofit.Builder&#40;&#41;)

[//]: # (    .baseUrl&#40;API_BASE_URL&#41;)

[//]: # (    .client&#40;OkHttpClient.Builder&#40;&#41;.build&#40;&#41;&#41;)

[//]: # (    .addConverterFactory&#40;JacksonConverterFactory.create&#40;objectMapper&#41;&#41;)

[//]: # (    .build&#40;&#41;)

[//]: # (    .create<CatFactClient>&#40;&#41;)

[//]: # (```)

[//]: # ()
[//]: # (Now, our business logic:)

[//]: # ()
[//]: # (```kotlin)

[//]: # (override suspend fun get&#40;numberOfFacts: Int&#41;: Set<Fact> =)

[//]: # (    coroutineScope {)

[//]: # (        &#40;1..numberOfFacts&#41;.map {)

[//]: # (            async { client.fact&#40;&#41; })

[//]: # (        }.awaitAll&#40;&#41;)

[//]: # (            .map { Fact&#40;it.fact&#41; })

[//]: # (            .toSet&#40;&#41;)

[//]: # (    })

[//]: # (```)

[//]: # ()
[//]: # (This function concurrently dispatches `numberOfFacts` calls to the API, awaits all replies, translates them into the domain model, and returns a fact set. We utilize `Set` over `List` since the API doesn't assure unique responses.)

[//]: # ()
[//]: # (Inspect the finalized version of the code [here]&#40;https://github.com/yonatankarp/cat-fact-client/blob/implement-fetch-cat-facts/src/main/kotlin/cat/fact/client/CatFactProvider.kt&#41;.)

[//]: # ()
[//]: # (![Happy Cat]&#40;https://cdn.hashnode.com/res/hashnode/image/upload/v1693143872825/kKuvJTSlm.webp?auto=format align="center"&#41;)

[//]: # ()
[//]: # (This piece isn’t tailored to guide library publishing. However, if you’re inclined, relevant settings can be found [here]&#40;https://github.com/yonatankarp/cat-fact-client/blob/implement-fetch-cat-facts/build.gradle.kts&#41;.)

[//]: # ()
[//]: # (Our library's artifact, version `0.1.0`, is available on GitHub packages and awaits your exploration. An updated version &#40;`0.2.0`&#41; offers mock implementations, bypassing internet prerequisites with a few breaking changes. Nevertheless, the core remains unaltered.)

[//]: # ()
[//]: # (%%[contact-me])

[//]: # ()
[//]: # (## Conclusion)

[//]: # ()
[//]: # (This article provides a comprehensive guide on building a client library for fetching cat facts from an API, using Kotlin, Gradle, Retrofit, and Jackson. The library is designed to handle multiple concurrent API calls and return a set of unique facts. The implementation of OpenTelemetry and service instrumentation is planned for the next parts of this series, ultimately enhancing the observability of the service.)

[//]: # ()
[//]: # (## Acknowledgments)

[//]: # ()
[//]: # (* [Mariusz Sołtysiak]&#40;https://medium.com/@mariuszsoltysiak&#41; - for moral support, review, and suggestions while writing this series of articles.)
