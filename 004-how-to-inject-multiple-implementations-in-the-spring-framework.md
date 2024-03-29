---
title: "How to inject multiple implementations in the Spring Framework"
subtitle: "Simplifying Code with Dependency Injection: The Spring Magic"
slug: how-to-inject-multiple-implementations-in-the-spring-framework
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: guide, kotlin, springboot, kotlin-beginner, solid-principles
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/MfnX4XtGnvU/upload/v1644563552394/x8pQZB0mQ.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Inject Multiple Implementations in Spring Framework: Simplify Code and"
seoDescription: "Learn how to inject multiple implementations in Spring Framework, supporting multiple languages and simplifying your code for improved scalability."
seriesSlug: spring-boot
# Table of contents
enableToc: false
saveAsDraft: false
---

Recently, I had a discussion with one of my colleagues during a code review. We talked about a hidden gem in Spring (as well as other frameworks like Micronaut) that could simplify our code.

Let's consider a task: developing a good morning greeting system that supports both English and Hebrew languages. The system should greet us in each of these languages.

We'll begin with the native implementation by introducing our greeters:

```Kotlin
class HebrewGreeter {
    fun sayGoodMorning() {
        println("בוקר טוב")
    }
}

class EnglishGreeter {
    fun sayGoodMorning() {
        println("Good morning")
    }
}
```

Next, we'll create a class to hold the greeters and invoke them:

```Kotlin
@Service
class GreeterService(
    private val hebrewGreeter: HebrewGreeter = HebrewGreeter(),
    private val englishGreeter: EnglishGreeter = EnglishGreeter()
) {
    fun greetInAllLanguages() {
        hebrewGreeter.sayGoodMorning()
        englishGreeter.sayGoodMorning()
    }
}
```

This implementation works as expected. However, imagine that we want to introduce a new greeter, such as a German greeter. In that case, we would need to modify the `GreeterService` class, which violates the [open-closed principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) from the SOLID principles.

To address this issue, we'll introduce an `interface` that encapsulates the common functionality of all greeters:

```Kotlin
interface Greeter {
    fun sayGoodMorning()
}

@Component
class HebrewGreeter : Greeter {
    override fun sayGoodMorning() {
        println("בוקר טוב")
    }
}

@Component
class EnglishGreeter : Greeter {
    override fun sayGoodMorning() {
        println("Good morning")
    }
}

@Component
class GermanGreeter : Greeter {
    override fun sayGoodMorning() {
        println("Guten Morgen")
    }
}
```

Now comes the exciting part. Since we've implemented an interface, the `GreeterService` can request Spring to inject all available implementations into its constructor:

```Kotlin
@Service
class GreeterService(private val greeters: List<Greeter>) {
    fun greetInAllLanguages() {
        greeters.forEach { it.sayGoodMorning() }
    }
}
```

Our code is now much simpler, but that's not the only benefit. If we introduce a new greeter in the future, it will be automatically added to the system without requiring any modifications to the code!

Another option instead of using `List<Greeter>` would be to use a `Map<String, Greeter>`. In this case, Spring would inject a map where the keys are the full class names and the values are the corresponding beans. For example, `com.example.greeter.EnglishGreeter`.

%%[contact-me]
