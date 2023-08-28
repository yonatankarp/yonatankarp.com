---
title: "Kotlin Code Smell 20 - Settings/Configs"
subtitle: "Settings Spaghetti: When Configurations Go Wild..."
slug: kotlin-code-smell-20-settingsconfigs
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/REZp_5-2wzA/upload/68a1cba84b003c37b553a9dfd0a20256.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Optimizing Software Behavior: Polymorphic Objects Avoiding Duplication"
seoDescription: "Improve software behavior with polymorphic objects. Learn how to avoid duplicated code and enhance testability for clean, declarative systems."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


## Problem

* Duplicated Code
    
* [If Pollution](https://maximilianocontieri.com/how-to-get-rid-of-annoying-ifs-forever)
    
* Global usage
    
* [Coupling](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem)
    
* Testability and the explosion of testing scenarios
    
* Complexity
    

## Solution

* Avoid using settings directly
    
* Create polymorphic objects and inject them externally.
    

## Sample Code

### Wrong

```kotlin
class VerySpecificAndSmallObjectThatDealWithPersistence {
    fun retrieveData() =
        if (GlobalSettings.instance["I am A Possible Mistyped String"] != null) {
            retrieveDataThisWay()
        } else {
            retrieveDataTheOtherWay()
        }

    private fun retrieveDataThisWay() {
        // ...
    }
    
    private fun retrieveDataTheOtherWay() {
        // ...
    }
}

class GlobalSettings {
    private val settings = mutableMapOf<String, String>()

    operator fun set(key: String, value: String) {
        settings[key] = value
    }
    
    operator fun get(key: String) = settings[key]

    companion object {
        val instance = GlobalSettings()
    }
}
```

### Right

```kotlin
class VerySpecificAndSmallObjectThatDealWithPersistence(private val retrieveStrategy: RetrieveStrategy) {
    fun retrieveData() = retrieveStrategy.retrieve()
}

interface RetrieveStrategy {
    fun retrieve(): String
}

class RetrieveFromDatabase : RetrieveStrategy {
    override fun retrieve(): String {
        return "Data from the database"
    }
}
```

## Examples

* External Connection Settings
    
* User settings
    
* [Feature Toggle](https://en.wikipedia.org/wiki/Feature_toggle)
    

## Exceptions

* Sometimes, we use Feature toggling as a safeguard mechanism. This is acceptable in a legacy system. However, these toggles should be very short-lived in a [CI/CD](https://en.wikipedia.org/wiki/CI/CD) system.
    
* Hyperparameter settings should be managed by configuration objects. You can read these objects from any persistence media and change your system behavior at runtime in an explicit and controlled way.
    

## Conclusion

Setting runtime behavior is great for software systems.

We should configure our objects so they can behave in different ways, and we should achieve it in an explicit way with explicit behavioral objects.

In this way, our code will be more declarative, clean, and testable. It is not as easy as adding an IF Statement. This kind of lazy developer brings lots of coupling and unexpected issues to our systems.

> A system with 300 Boolean configurations has more test combinations (2 ^ 300) than the number of atoms in the universe (10 ^ 80).

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 29 - Settings/Configs](https://maximilianocontieri.com/code-smell-29-settingsconfigs) by @[Maximiliano Contieri](@mcsee)
