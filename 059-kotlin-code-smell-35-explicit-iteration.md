---
title: "Kotlin Code Smell 35 - Explicit Iteration"
subtitle: "While loops are foundational, enumerators and iterators represent progression."
slug: kotlin-code-smell-35-explicit-iteration
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/FOsina4f7qM/upload/c37139d31191c8b8ab90c24697abff6e.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Coding Techniques: Moving from Explicit to Declarative Iteratio"
seoDescription: "Boost your Kotlin coding skills by eliminating explicit iteration and embracing higher-order collection functions for cleaner, more efficient code."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> ***TL;DR:*** Avoid index-based iteration. Embrace higher-order collection functions.

## Problem

* Violation of encapsulation

* Lack of declarativeness


## Solution

* Opt for `forEach()` or high-order iterators.

* Concealing implementation details opens up possibilities like caching, proxies, lazy loading, and more.


## Sample Code

### Wrong

```kotlin
for(i in 0 until colors.count()) {
    print(colors[i])
}

// For Kotlin 1.9 and above, the 'until' can (and should) be
// substituted with '..<' to denote a range from 0 to 
// colors.count(), excluding the end.
```

### Right

```kotlin
for(color in colors) {
    println(color)
}

// Utilizing closures and arrow functions
colors.forEach { println(it) }
```

## Exceptions

Should the problem domain necessitate elements being mapped to natural numbers like indices, then the initial method may suffice.

Always strive to draw parallels with real-world scenarios.

%%[contact-me]

## Conclusion

Many developers overlook this kind of code smell, dismissing it as a minor detail.

Yet, it's the accumulation of such declarative nuances that truly elevates code quality.


## Credits

* [Code Smell 53 - Explicit Iteration](https://maximilianocontieri.com/code-smell-53-explicit-iteration) by @[Maximiliano Contieri](@mcsee)
