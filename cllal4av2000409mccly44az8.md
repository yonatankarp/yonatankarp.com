---
title: "Kotlin Code Smell 28 - Abstract Names"
seoTitle: "Mastering Kotlin Naming Conventions: Overcoming Abstract Names in Your"
seoDescription: "Optimize your Kotlin code by avoiding abstract names and using meaningful naming conventions, improving code quality, readability, and maintainability."
datePublished: Mon Aug 14 2023 08:00:09 GMT+0000 (Coordinated Universal Time)
cuid: cllal4av2000409mccly44az8
slug: kotlin-code-smell-28-abstract-names
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/wQLAGv4_OYs/upload/0da07320cfbf4e807972d868892c17a3.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

---

## Problem

* Implemental Naming
* Meaningless names
* Broken [**MAPPER**](https://maximilianocontieri.com/what-is-wrong-with-software) and [**Bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) to real-world entities.

## Solution

1. Choose meaningful names.
2. Find metaphors.
3. Avoid words like *abstract*, *base*, *generic*, *helper*, *data*, *info*, etc.
4. Use [**rules**](https://maximilianocontieri.com/what-exactly-is-a-name-part-ii-rehab) for naming.

## Sample Code

### Wrong

```kotlin
class Repository {
    // ...
}

class MeetingCollection {
    // ...
}

class AccountsComposite {
    // ...
}

class NoteArray {
    // ...
}

class LogCollector {
    // ...
}

abstract class SearcherBase {
    // ...
}

abstract class AbstractTransportation {
    // ...
}
```

### Right

```kotlin
class Schedule {
    // ...
}

class Portfolio {
    // ...
}

class NoteBook {
    // ...
}

class Journal {
    // ...
}

class Vehicle {
    // ...
}
```

## Conclusion

Finding names is the last thing we should do in our designs. Unless we have a clear business understanding, good names emerge at the end after defining behavior and protocol boundaries.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 38 - Abstract Names](https://maximilianocontieri.com/code-smell-38-abstract-names) by @[Maxi Contieri](@mcsee)