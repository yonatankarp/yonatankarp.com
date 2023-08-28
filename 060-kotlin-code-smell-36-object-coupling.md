---
title: "Kotlin Code Smell 36 - Object Coupling"
subtitle: "Object Boundaries Respected: Navigating Coupling Challenges"
slug: kotlin-code-smell-36-object-coupling
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/yB2TGLr-rVo/upload/e62a1c9cd1c1ac241af55927059db55a.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Object Coupling in Kotlin: Tips for Cleaner, More Maintainable Code"
seoDescription: "Boost Kotlin code quality by avoiding object coupling, respecting encapsulation, and prioritizing interfaces for cleaner, maintainable projects."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: true
---

> **TL;DR:** Respect object boundaries: avoid coupling to data and prioritize interfaces and behavior.

* When you view your objects merely as data holders, you risk violating their encapsulation.


## Problem

* Information Hiding Violation

* Encapsulation Violation

* Coupling


## Solution

* Always couple to interfaces and behavior, not data.


## Sample Code

### Wrong

```kotlin
data class Point(var x: Double, var y: Double)

class DistanceCalculator {
    fun distanceBetween(origin: Point, destination: Point): Double {
        return sqrt(
            (destination.x - origin.x).pow(2) +
                    (destination.y - origin.y).pow(2)
        )
    }
}
```

### Right

```kotlin
data class Point(
    private val radius: Double,
    private val theta: Double
) {
    val x: Double get() = radius * cos(theta)
    val y: Double get() =  radius * sin(theta)
}

class DistanceCalculator {
    fun distanceBetween(origin: Point, destination: Point): Double {
        return sqrt(
            (destination.x - origin.x).pow(2) +
                    (destination.y - origin.y).pow(2)
        )
    }
}
```

%%[contact-me]

## Conclusion

If your classes are polluted with setters, getters and public methods you will certainly have ways to couple to their accidental implementation.


## Credits

* [Code Smell 55 - Object Orgy](https://maximilianocontieri.com/code-smell-55-object-orgy) by @[Maximiliano Contieri](@mcsee)
