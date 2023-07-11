---
title: "Kotlin Code Smell 17 - Pattern Abusers"
seoTitle: "Avoid Overusing Patterns: Kotlin Code Smell 17"
seoDescription: "Prevent overusing patterns in Kotlin to enhance code readability. Choose real-world names and strategically apply patterns for optimal results."
datePublished: Sat Mar 11 2023 05:02:29 GMT+0000 (Coordinated Universal Time)
cuid: clf3i2xhq000l09mk1l95egtt
slug: kotlin-code-smell-17-pattern-abusers
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670955993196/RxaGcdqBJ.webp
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

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

* [Code Smell 25 - Pattern Abusers](https://maximilianocontieri.com/code-smell-25-pattern-abusers) by Maxi Contieri