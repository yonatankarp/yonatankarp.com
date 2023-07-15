---
title: "Kotlin Code Smell 1 - Constants and Magic Numbers"
seoTitle: "Avoid Constants and Magic Numbers in Kotlin Code"
seoDescription: "Learn why using constants and magic numbers in Kotlin code can lead to issues. Find solutions to improve code quality and maintainability."
datePublished: Wed Nov 30 2022 18:52:36 GMT+0000 (Coordinated Universal Time)
cuid: clb409fr5000108le5ydlh8ee
slug: kotlin-code-smell-001-constants-and-magic-numbers
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/0V7_N62zZcU/upload/v1669794404946/-lX9JMj_h.jpeg
tags: programming-blogs, kotlin, kotlin-beginner, code-smell-1

---

> **TL;DR:** Avoid using magic numbers without explanation. Their source is unknown, and we fear changing them.

## Problems

* Coupling
    
* Low testability
    
* Low readability
    

## Solutions

* Rename the constant with a meaningful and intention-revealing name.
    
* Replace constants with parameters, allowing them to be mocked from the outside.
    
* The constant definition should be separate from its usage.
    

## Examples

* Algorithms Hyper Parameters
    

## Sample Code

### Wrong

```kotlin
fun energy(mass: Double) = (mass * 299792458).pow(2)
```

### Right

```kotlin
typealias MetersPerSeconds = Int

// Using a specific unit of measure to avoid another code smell
const val LIGHT_SPEED: MetersPerSeconds = 299792458

fun energy(mass: Double) = (mass * LIGHT_SPEED).pow(2)
```

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More Information

* [Refactoring Guru](https://refactoring.guru/es/replace-magic-number-with-symbolic-constant)
    
* [How to Decouple a Legacy System](https://maximilianocontieri.com/how-to-decouple-a-legacy-system)
    

## Credits

* [Code Smell 02 - Constants and Magic Numbers](https://maximilianocontieri.com/code-smell-02-constants-and-magic-numbers) by @[Maxi Contieri](@mcsee)