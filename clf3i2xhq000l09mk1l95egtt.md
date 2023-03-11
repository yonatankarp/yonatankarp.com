---
title: "Kotlin Code Smell 017 - Pattern Abusers"
seoTitle: "Kotlin Code Smell 017 - Pattern Abusers | by Yonatan Karp-Rudin"
datePublished: Sat Mar 11 2023 05:02:29 GMT+0000 (Coordinated Universal Time)
cuid: clf3i2xhq000l09mk1l95egtt
slug: kotlin-code-smell-017-pattern-abusers
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670955993196/RxaGcdqBJ.webp
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

---

## **Problems**

*   Over Design
    
*   Readability
    

## Solutions

1.  Measure the tradeoff of patterns usage.
    
2.  Create solutions based on real-world names ([**essential**](https://maximilianocontieri.com/no-silver-bullet)) over architecture (accidental).
    
3.  Choose [**good names**](https://maximilianocontieri.com/what-exactly-is-a-name-part-ii-rehab).
    
4.  User [**MAPPER**](https://maximilianocontieri.com/what-is-wrong-with-software) technique to find [**bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) real entities.
    

## Sample Code

### Wrong

```kotlin
class FileTreeComposite {
    // name should be inferred from behaviour
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
    // These names map 1:1 to real world concepts
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

## **Conclusion**

Chose when to apply a pattern solution. You are not being [smarter](https://yonatankarp.com/kotlin-code-smell-005-too-clever-for-your-own-good) by using too many patterns. You are smart if you choose the right opportunity to use the patterns.

## Credits

*   [Code Smell 25 - Pattern Abusers](https://maximilianocontieri.com/code-smell-25-pattern-abusers) by @[Maxi Contieri](@mcsee)