---
title: "Kotlin Code Smell 6 - Boolean Variables"
subtitle: "Rethinking Booleans: Embrace Polymorphism for Cleaner, Extendable Code"
slug: kotlin-code-smell-6-boolean-variables
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/76H1lEBFnqc/upload/v1669914752587/o85ecKiUs.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Boolean Variables: Use Polymorphic States"
seoDescription: "Learn why using boolean variables as flags in code leads to issues and how to improve extensibility with polymorphic states."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> ***TL;DR:*** Avoid using boolean variables, as they lead to the use of if statements. Instead, create polymorphic states.

# Problems

* Lack of extensibility
    
* Comparison issues in some languages
    

# Solutions

* If a boolean variable maps to a real-world entity, it is acceptable. Otherwise, model it as a state to promote extensibility. This approach aligns with the [**Open/Closed Principle**](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).
    

# Examples

* Usage of flags
    

# Exceptions

* Real-world true/false rules
    

# Sample Code

### wrong

```kotlin
fun processBatch(
    useLogin: Boolean,
    deleteEntries: Boolean,
    beforeToday: Boolean
) {
    ...
}
```

### Right

```kotlin
fun processBatch(
    useLogin: LoginStrategy,
    deleteEntries: DeletePolicy,
    beforeToday: OffsetDateTime
) {
    ...
}
```

%%[contact-me]

# Conclusion

Exercise caution when using booleans. Flags make code maintenance and extension challenging. Gain a better understanding of the domain and consider migrating to the [**state design pattern**](https://en.wikipedia.org/wiki/State_pattern). Utilize polymorphism instead of if statements or when expressions (pattern matching).

# More Info

* [FlagArgument](https://martinfowler.com/bliki/FlagArgument.html) by Martin Fowler
    

# Credits

* [Code Smell 07 - Boolean Variables](https://maximilianocontieri.com/code-smell-07-boolean-variables) by @[Maximiliano Contieri](@mcsee)
