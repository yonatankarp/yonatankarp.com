---
title: "Kotlin Code Smell 17 - Pattern Abusers"
subtitle: "Beyond Buzzwords: The Art of Thoughtful Pattern Application"
slug: kotlin-code-smell-17-pattern-abusers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670955993196/RxaGcdqBJ.webp
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Overusing Patterns: Kotlin Code Smell 17"
seoDescription: "Prevent overusing patterns in Kotlin to enhance code readability. Choose real-world names and strategically apply patterns for optimal results."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


## Problems

* Over Design
    
* Readability
    

## Solutions

1. Measure the tradeoff of pattern usage.
    
2. Create solutions based on real-world names ([**essential**](https://maximilianocontieri.com/no-silver-bullet)) over accidental architecture.
    
3. Choose [**good names**](https://maximilianocontieri.com/what-exactly-is-a-name-part-ii-rehab).
    
4. Use the [**MAPPER**](https://maximilianocontieri.com/what-is-wrong-with-software) technique to find [**bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) real entities.
    

## Sample Code

### Wrong

```kotlin
class FileTreeComposite {
    // name should be inferred from behavior
}

class DateTimeConverterAdapterSingleton {
    // ...
}

class PermutationSorterStrategy {
    // ...
}

class NetworkPacketObserver {
    // ...
}

class AccountsComposite {
    // ...
}
```

### Right

```kotlin
class FileSystem {
    // These names map 1:1 to real-world concepts
}

class DateTimeFormatter {
    // ...
}

class BubbleSort {
    // ...
}

class NetworkSniffer {
    // ...
}

class Portfolio {
    // ...
}
```

## Conclusion

Choose when to apply a pattern solution. You are not being [smarter](https://yonatankarp.com/kotlin-code-smell-005-too-clever-for-your-own-good) by using too many patterns. You are smart if you choose the right opportunity to use the patterns.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 25 - Pattern Abusers](https://maximilianocontieri.com/code-smell-25-pattern-abusers) by @[Maxi Contieri⭐⭐⭐](@mcsee)
