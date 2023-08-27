---
title: "Kotlin Code Smell 23 - Singletons"
subtitle: "One Too Many: The Slippery Slope of Singleton Patterns"
slug: kotlin-code-smell-23-singletons
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/4CdIv6SZkck/upload/02fa10cef1c8aea07fab21dab0baa64f.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Code Smell 23: Singletons - Harmful Design Pattern"
seoDescription: "Discover the problems of singletons in Kotlin, from coupling to memory issues. Learn better solutions for improved software designץ"
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


## Problem

* [Coupling](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem)
    
* Testability
    
* Accidental implementation problems
    
* Multi-threading issues
    
* Static methods polluting
    
* Object creation contract violation
    
* [Bijection](https://mcsee.hashnode.dev/the-one-and-only-software-design-principle) mismatch
    
* Memory issues
    
* Premature Optimization
    

## Solution

1. Avoid singletons altogether.
    
2. Use contextual unique objects instead of relying on a global instance.
    
3. Benchmark object creation to ensure performance is not adversely affected.
    

## Examples

* Database Access
    
* Globals
    
* Loggers
    
* Helper classes
    

## Sample Code

### Wrong

```kotlin
// God is the archetypal singleton example
class God {
    // In Kotlin, the companion object is always a singleton
    companion object {
        private var instance: God? = null

        fun getInstance(): God {
            if (instance == null) {
                instance = God()
            }
            
            return requireNotNull(instance)
        }
    }
}

// Why should we be aware of getInstance when creating an object?
val jewishGod = God.getInstance()
```

### Right

```kotlin
interface Religion {
    // Define behavior
}

class God {
    // There can be as many as you wish
}

class PolytheisticReligion(private val gods: Collection<God>) : Religion
class MonotheisticReligion(private val godAllMighty: God) : Religion

// According to Judaism, there's only one God,
// but this does not hold in other religions.
// Under this context, God is unique. We cannot create or change
// a new one.
val jewishGod = God()
val judaism = MonotheisticReligion(jewishGod)

val jupiter = God()
val saturn = God()
val mythologicalReligion = PolytheisticReligion(listOf(jupiter, saturn))

// Gods are unique (or not) according to context.
// We can create test religions with or without unicity.
// This is less coupled since we break the direct reference to the
// God class.
// The God class has the single responsibility to create gods, not
// to manage them.
```

## Conclusion

The use of singletons is a historical mistake that has already been acknowledged by the community. Nevertheless, lazy developers bring it up again and again. We need to reach a consensus on its drawbacks and strive for better design patterns.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 32 - Singletons](https://maximilianocontieri.com/code-smell-32-singletons) by @[Maxi Contieri⭐⭐⭐](@mcsee)
