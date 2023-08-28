---
title: "Kotlin Code Smell 31 - Not Polymorphic"
subtitle: "A Path to Clearer Code and Stronger Design"
slug: kotlin-code-smell-31-not-polymorphic
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1691137175613/31685090-0c41-47c5-915c-102997e11f33.avif
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Programming Guide: Clean Code with Polymorphic Solutions"
seoDescription: "Improve Kotlin skills by avoiding code smell with polymorphism for cleaner, efficient code. Learn best practices and enhance your projects."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
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

%%[contact-me]

## Credits

* [Code Smell 45 - Not Polymorphic](https://maximilianocontieri.com/code-smell-45-not-polymorphic) by @[Maximiliano Contieri](@mcsee)
