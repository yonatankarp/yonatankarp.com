---
title: "Kotlin Code Smell 6 - Boolean Variables"
seoTitle: "Avoid Boolean Variables: Use Polymorphic States"
seoDescription: "Learn why using boolean variables as flags in code leads to issues and how to improve extensibility with polymorphic states."
datePublished: Tue Dec 06 2022 08:00:42 GMT+0000 (Coordinated Universal Time)
cuid: clbbxm7az05ahycnvfnjr4fgm
slug: kotlin-code-smell-6-boolean-variables
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/76H1lEBFnqc/upload/v1669914752587/o85ecKiUs.jpeg
tags: programming-blogs, kotlin, kotlin-beginner, code-smell-1

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

# Conclusion

Exercise caution when using booleans. Flags make code maintenance and extension challenging. Gain a better understanding of the domain and consider migrating to the [**state design pattern**](https://en.wikipedia.org/wiki/State_pattern). Utilize polymorphism instead of if statements or when expressions (pattern matching).

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

# More Info

* [FlagArgument](https://martinfowler.com/bliki/FlagArgument.html) by Martin Fowler
    

# Credits

* [Code Smell 07 - Boolean Variables](https://maximilianocontieri.com/code-smell-07-boolean-variables) by @[Maxi Contieri⭐⭐⭐](@mcsee)
