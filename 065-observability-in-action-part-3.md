---
title: "Observability in Action Part 3: Enhancing Your Codebase with OpenTelemetry"
subtitle: "A Step-by-Step Guide to Instrumenting a Kotlin Spring-Boot Service with OpenTelemetry"
slug: observability-in-action-part-3-enhancing-your-codebase-with-opentelemetry
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: kotlin, observability, opentelemetry, devops, springboot
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/z9z6u1rn7sY/upload/64059cfb860d42a549748c2756ca7a5a.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Boost Your Codebase with OpenTelemetry: A Comprehensive Guide"
seoDescription: "Improve codebase performance with OpenTelemetry and Honeycomb.io, boosting observability, monitoring, and insights for modern applications."
seriesSlug: spring-boot
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Enhancing your codebase with OpenTelemetry involves setting up [HoneyComb.io](http://HoneyComb.io), integrating the OpenTelemetry SDK and agent, modifying the bootRun task, running the service locally, updating the Dockerfile, and modifying the docker-compose.yml file. This allows you to monitor your service and gain insights into its behavior.

Welcome to my series on Observability in Action. In this series, I explore various aspects of enhancing your codebase with modern observability techniques. If you're new to the series, I highly encourage you to check out our previous articles to gain a comprehensive understanding of the topics covered.

### Series Outline

* [Part 1 - Build Client Library](https://yonatankarp.com/observability-in-action-part-1-enhancing-your-codebase-with-opentelemetry)

* [Part 2 - Build the Service](https://yonatankarp.com/observability-in-action-part-2-enhancing-your-codebase-with-opentelemetry)

* [Part 3 - Instrument the Service](https://yonatankarp.com/observability-in-action-part-3-enhancing-your-codebase-with-opentelemetry) ⬅ You are here

* Part 4 - Integrate the OpenTelemetry Collector


All code examples for this series are available on GitHub:

* Code examples related to this service can be found [here](https://github.com/yonatankarp/cat-fact-service/tree/add-instumentation) on the branch `add-instumentation`.


## Introduction to HoneyComb.io

In this article, we'll use [HoneyComb.io](https://www.honeycomb.io/) as our tracing backend. While there are other tools in the market, some of which can be run on your local machine (e.g., [Jaeger](https://www.jaegertracing.io/)), I chose HoneyComb because of their complementary tools that offer improved monitoring of the service and insights into its behavior.

HoneyComb is a cloud-based observability platform that helps developers gain insights into their software systems. They provide tools such as SLA/SLO monitoring, distributed tracing, and real-time log aggregation. This enables us to quickly identify and address problems before they affect users. HoneyComb operates on an event-based data model, which means engineers can explore and analyze data in real-time, drilling down into specific issues to identify the root cause and take corrective action. HoneyComb also offers visualization tools like heatmaps, histograms, and scatter plots, but we won't cover them in this series.

### Setting up HoneyComb.io

You can sign up for a free HoneyComb account that processes up to 20 million events per month, which is more than sufficient for our needs. To create an account, visit [HoneyComb.io](https://www.honeycomb.io/), click on the "Start for Free" button, fill in your information, and set up a team.

For this article, we will use the default `test` environment, but you can create additional environments as you see fit.

After setting up, you should land on a page that looks like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958050720/8c6013d6-0577-4ca7-a59d-188058f22fa5.png align="center")

Next, we'll create a new API key for our service to send data to HoneyComb. To do this, click on `Account` ➡️ `Team settings`. On the following page,

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958068374/3399e8b5-88ef-4ed3-8a01-1a516fe3c26d.png align="center")

under the `Environments and API Keys` section, click the `Manage` button.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958100375/2af5d489-fb60-488c-833c-b5638813fbe3.png align="center")

On the next page, click the `Create API Key` button and name it. For this tutorial, let's call it `local` (indicating local execution). We want to limit our key's scope to the minimum required, so the key should have only the `Send events` and `Create datasets` permissions.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958113397/b1682df7-021d-48da-b957-1e3e0c79b5c3.png align="center")

Once you've made these selections, click the `Save` button. You should now see the key displayed on your screen. We'll use this key later when configuring our service.

## Setting Up Instrumentation

This section will detail how to equip our service with OpenTelemetry, which is the primary focus of this article.

### Prerequisites

To achieve this, we will add the following to our project:

* OpenTelemetry Agent - for automatic tracing

* OpenTelemetry SDK - for manual tracing

* A Gradle task to fetch the OpenTelemetry Java agent before every build

* Configuration of the OpenTelemetry agent within the `bootRun` task for local testing

* Modifications to the `Dockerfile` to integrate the OpenTelemetry agent

* Modifications to the `docker-compose.yml` file to add the OpenTelemetry agent's environmental settings


Time to dive in!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958126625/e23898d8-cdf6-44e4-be9f-774a228ad7f2.gif align="center")

### Integrating the OpenTelemetry SDK and Agent

First, we'll add the OpenTelemetry SDK and agent dependencies to our project. HoneyComb provides a library that extends the basic functionality of the JVM OpenTelemetry, and we will use it.

#### Dependency Integration

We will start by adding our dependencies within `build.gradle.kts`:

```kotlin
dependencies {
  implementation("io.honeycomb:honeycomb-opentelemetry-sdk:1.5.2")
  // We're using compileOnly as we need this dependency only to set the
  // agent on our docker image and local development
  compileOnly("io.honeycomb:honeycomb-opentelemetry-javaagent:1.5.2")
}
```

#### Constructing the Gradle Task for the OpenTelemetry Agent

Once we're done, we will create a new Gradle task (called `copyOpenTelemetryAgent`) that will copy the OpenTelemetry agent to the `build/output/libs` directory before each build by making the `build` task depends on it.

```kotlin
tasks {
  build {
    dependsOn("copyOpenTelemetryAgent")
  }    
    
  register<Copy>("copyOpenTelemetryAgent") {
    project.delete(
        fileTree("${layout.buildDirectory.get().asFile}/output/libs")
    )

    from(configurations.compileClasspath)
    into("${layout.buildDirectory.get().asFile}/output/libs")
    include("honeycomb-opentelemetry-javaagent*")
    // We want to remove the version from the jar file name for easier
    // referencing during the service execution
    rename("-[1-9]+.[0-9]+.[0-9]+.jar", ".jar")
  }
}
```

You can refresh Gradle and see that the new task appears:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958182938/b84f2de5-2805-4754-8ba2-192662d63282.png align="center")

We can run the `build` task and see that the OpenTelemetry agent is copied to the `build/output/libs` directory:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958193898/68864760-9593-4d29-a085-b5422fc60c51.png align="center")

#### Modifying the `bootRun` Task

Next, we'll update the `bootRun` task to include the OpenTelemetry agent. By doing so, we can run the service locally and have it send data to HoneyComb.

Add the following to the `bootRun` task in your `build.gradle.kts` file:

```kotlin
tasks {
    bootRun {
        environment = mapOf(
            "HONEYCOMB_API_KEY" to System.getenv("HONEYCOMB_API_KEY"),
            "SERVICE_NAME" to "cat-fact-service",
            "HONEYCOMB_API_ENDPOINT" to "https://api.honeycomb.io:443",
            "ENVIRONMENT" to "test",
        )

        jvmArgs = listOf(
            "-javaagent:${layout.buildDirectory.get().asFile}/output/libs/honeycomb-opentelemetry-javaagent.jar",
            // Passing static parameter to the collector, in this case - 
            // a reference to the GitHub repository
            "-Dotel.resource.attributes=github.repository=https://github.com/ForkingGeniuses/cat-fact-service",
        )
    }
}
```

Note that to work, this task needs the `HONEYCOMB_API_KEY` environment variable to be set with the API key we created earlier. Moreover, currently, we're calling the HoneyComb API directly, so we need to set the `HONEYCOMB_API_ENDPOINT` environment variable to `https://api.honeycomb.io:443`. In the future, we'll fix this by using the OpenTelemetry collector.

#### Local Service Execution

We can now run the service, and observe the data being sent to HoneyComb.

To run our service, execute the following command (ensure that the database is running):

```bash
$ ./gradlew bootRun
```

Generate some data by accessing the endpoint a few times:

```bash
$ curl http://localhost:8080/api/v1/cat/facts
```

Go to the HoneyComb UI and click on the `Query` button. You can click the `Run Query` button to see the data being sent to HoneyComb. You can see for example our `github.repository` attribute being sent:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958235562/b824c4a4-1552-43d7-b99b-0a83d9bb434f.png align="center")

We can also create a graph to visualize the data by selecting anything under `VISUALIZE` box. For example:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958261190/6df2a461-2aae-4e59-bda2-823c1ef202fa.png align="center")

Lastly, we can drill down into a specific trace by clicking on the traces, and see the execution path of the request:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958268950/5b100bcd-5219-4f66-bc0e-a371b7869839.png align="center")

#### Modifications the Dockerfile

Running the service locally is great, but we want to run it in a container. By doing so, we can execute the service in a more production-like environment.

The changes required to run the service in a container are minimal. We need to add the OpenTelemetry agent to the container and set the environment variables required by the agent.

We will also add our static attributes to the agent, so we can easily filter the data in HoneyComb. We can have multiple attributes separated by a comma. For example:

```text
github.repository=https://github.com/yonatankarp/cat-fact-service,slack.channel=#cat-facts
```

Our updated `Dockerfile` will look like this:

```diff
 FROM --platform=linux/x86_64 eclipse-temurin:17-jre-alpine
 
 ENV APP_BASE="/home" \
     APP_NAME="cat-fact-service" \
+    OTEL_ATTRIBUTES="github.repository=https://github.com/yonatankarp/cat-fact-service" \
     SERVER_PORT="8080"
 
 EXPOSE ${SERVER_PORT}
 RUN apk update && apk upgrade && apk add curl openssl gcompat bash busybox-extra
 
 RUN mkdir -p ${APP_BASE}/${APP_NAME}
 
+# Otel agent
+COPY "/build/output/libs" "${APP_BASE}/${APP_NAME}"
+
+COPY "/build/libs/${APP_NAME}*.jar" "${APP_BASE}/${APP_NAME}.jar"
 

 CMD java $JAVA_OPTS \
+    -Dotel.resource.attributes="${OTEL_ATTRIBUTES}" \
+    -javaagent:${APP_BASE}/${APP_NAME}/honeycomb-opentelemetry-javaagent.jar \
     -jar ${APP_BASE}/${APP_NAME}.jar
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692958311701/fac8c12f-6d50-4b9c-bb90-08efa3aab9fd.png align="center")

#### Modifications of the `docker-compose.yml` File

Lastly, the `docker-compose.yml` will need updating to add the required environment variables:

```diff
@@ -16,6 +16,10 @@ services:
       - DB_NAME=facts
       - DB_USER=postgres
       - DB_PASSWORD=secret
+      - HONEYCOMB_API_KEY=${HONEYCOMB_API_KEY}
+      - SERVICE_NAME=cat-fact-service
+      - HONEYCOMB_API_ENDPOINT=https://api.honeycomb.io:443
+      - OTEL_JAVAAGENT_DEBUG=false
 
   postgres:
     container_name: cat-fact-service-postgres
```

Activate the entire configuration using `docker-compose`:

```bash
$ docker compose up
```

%%[contact-me]

## Conclusion

Enhancing your codebase with OpenTelemetry allows you to monitor your service and gain valuable insights into its behavior. By integrating [HoneyComb.io](http://HoneyComb.io), the OpenTelemetry SDK, and the agent, you can effectively set up observability for your service while also benefiting from HoneyComb's powerful analysis tools. This process involves updating the bootRun task, Dockerfile, and docker-compose.yml file, as well as setting up [HoneyComb.io](http://HoneyComb.io) for tracing.

In the next article, we will show how to correctly instrument a Spring filter for our service.

## Acknowledgments

* [Mariusz Sołtysiak](https://medium.com/@mariuszsoltysiak) - for moral support, review, and suggestions while writing this series of articles.
