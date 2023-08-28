---
title: "Kotlin Code Smell 1 - Constants and Magic Numbers"
subtitle: "A method makes calculations with many numbers without describing their semantics"
slug: kotlin-code-smell-1-constants-and-magic-numbers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/0V7_N62zZcU/upload/v1669794404946/-lX9JMj_h.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Constants and Magic Numbers in Kotlin Code"
seoDescription: "Learn why using constants and magic numbers in Kotlin code can lead to issues. Find solutions to improve code quality and maintainability."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
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

%%[contact-me]

## More Information

* [Refactoring Guru](https://refactoring.guru/es/replace-magic-number-with-symbolic-constant)
    
* [How to Decouple a Legacy System](https://maximilianocontieri.com/how-to-decouple-a-legacy-system)
    

## Credits

* [Code Smell 02 - Constants and Magic Numbers](https://maximilianocontieri.com/code-smell-02-constants-and-magic-numbers) by @[Maximiliano Contieri](@mcsee)
