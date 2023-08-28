---
title: "Kotlin Code Smell 27 - Protected Attributes"
subtitle: "SOLID Solutions: Beyond Sub-Classification Struggles"
slug: kotlin-code-smell-27-protected-attributes
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Uf-c4u1usFQ/upload/e5894315f656ab2940d08f90a8558c1c.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Guide: Eliminating Protected Attributes for Cleaner Code"
seoDescription: "Reduce the length of your text by eliminating unnecessary words, using shorter sentences, and focusing on the most important points."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

## Problem

* [**Sub-classification**](https://maximilianocontieri.com/code-smell-11-subclassification-for-code-reuse) for code reuse purposes.
* [**Liskov substitution**](https://en.wikipedia.org/wiki/Liskov_substitution_principle) violation ([**SOLID**](https://en.wikipedia.org/wiki/SOLID) principle).
* Possible subclass overrides.

## Solution

1. Favor composition
2. Avoid subclassifying attributes.
3. Extract behavior to separate objects.

## Sample Code

### Wrong

```kotlin
abstract class ElectronicDevice(protected val battery: Battery)

abstract class IDevice(
    battery: Battery,
    protected val operatingSystem: OperatingSystem
) : ElectronicDevice(battery)

class IPad(
    battery: Battery,
    ios: OperatingSystem
) : IDevice(battery, ios)

class IPhone(
    battery: Battery,
    ios: OperatingSystem,
    val phoneModule: PhoneModule
) : IDevice(battery, ios)
```

### Right

```kotlin
interface ElectronicDevice {
    //...
}

interface PhoneCommunication {
    //...
}

class IPad(
    private val battery: Battery,
    private val operatingSystem: OperatingSystem
) : ElectronicDevice {
    // Implement iPad-specific functionality here
}

class IPhone(
    private val battery: Battery,
    private val operatingSystem: OperatingSystem,
    private val phoneModule: PhoneModule
) : ElectronicDevice, PhoneCommunication {
    // Implement iPhone-specific functionality here
}
```

%%[contact-me]

## Conclusion

Protected attributes are yet another tool we should use carefully. Every decision is a smell, and we should be very cautious with attributes and inheritance.


## Credits

* [Code Smell 37 - Protected Attributes](https://maximilianocontieri.com/code-smell-37-protected-attributes) by @[Maximiliano Contieri](@mcsee)
