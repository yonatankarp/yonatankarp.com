---
title: "Kotlin Code Smell 31 - Not Polymorphic"
seoTitle: "Kotlin Programming Guide: Clean Code with Polymorphic Solutions"
seoDescription: "Improve Kotlin skills by avoiding code smell with polymorphism for cleaner, efficient code. Learn best practices and enhance your projects."
datePublished: Mon Aug 21 2023 08:34:11 GMT+0000 (Coordinated Universal Time)
cuid: cllkmf1hu001i08l6fwz26wfh
slug: kotlin-code-smell-31-not-polymorphic
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1691137175613/31685090-0c41-47c5-915c-102997e11f33.avif
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

---

## Problems

* Missed Polymorphism
* Coupling
* IFs / Type check Polluting.
* Names are coupled to types.

## Solutions

1. Rename methods after what they do.
2. Favor polymorphism.

## Sample Code

### Wrong

```kotlin
class Array {
    fun arraySort() { ... }
}

class List {
    fun listSort() { ... }
}

class Set {
    fun setSort() { ... }
}
```

### Right

```kotlin
interface Sortable {
    fun sort()
}

class Array : Sortable {
    override fun sort() { ... }
}

class List : Sortable {
    override fun sort() { ... }
}

class Set : Sortable {
    override fun sort() { ... }
}
```

## Conclusion

Naming is very important. We need to name concepts after what they do, not after accidental types.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 45 - Not Polymorphic](https://maximilianocontieri.com/code-smell-45-not-polymorphic) by @[Maxi Contieri](@mcsee)