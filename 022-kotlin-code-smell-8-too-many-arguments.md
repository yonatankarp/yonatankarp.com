---
title: "Kotlin Code Smell 8 - Too Many Arguments"
subtitle: "Optimizing Function Design: The Art of Grouping and Simplifying Arguments"
slug: kotlin-code-smell-8-too-many-arguments
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: functions, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670056643473/FcmfHdWvev.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Code Smell: Avoid Too Many Arguments"
seoDescription: "Learn how to improve your code quality by reducing the number of arguments in Kotlin functions. Enhance maintainability and readability."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Avoid passing more than three arguments to your functions.

## Problems

* Low maintainability.
    
* Low reusability.
    
* Coupling.
    
* Readability.
    

## Solutions

* Identify cohesive relationships among arguments.
    
* Create a "context" or related group.
    
* Consider using the [Method Object](https://wiki.c2.com/?MethodObject) Pattern.
    
* Avoid using "basic" types such as strings, arrays, and integers, and instead think in terms of objects.
    

## Exceptions

* Operations in the real world that do not require cohesive collaborators.
    

## Sample Code

### Wrong

```kotlin
class Printer {
    fun print(
        documentToPrint: String,
        paperSize: String,
        orientation: String,
        grayScales: Boolean,
        pageFrom: Int,
        pageTo: Int,
        copies: Int,
        marginLeft: Float,
        marginRight: Float,
        marginTop: Float,
        marginBottom: Float
    ): Unit = TODO()
}
```

### Right

```kotlin
class PaperSize {
    //...
}

class Document {
    //...
}

class PrintMargins {
    //...
}

class PrintRange {
    //...
}

class ColorConfiguration {
    //...
}

class PrintOrientation {
    //...
}

class PrintSetup(
    paperSize: PaperSize,
    orientation: PrintOrientation,
    color: ColorConfiguration,
    range: PrintRange,
    copiesCount: Int,
    margins: PrintMargins
)

class Printer {
    fun print(
        documentToPrint: Document,
        setup: PrintSetup
    ): Unit = TODO()
}
```

## Conclusion

To improve code quality, identify and group related arguments. Aim for real-world mappings and cohesive objects.

If a function requires too many arguments, some of them might be better suited for class construction. This is also a design issue.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More Info

* [Refactoring Guru](https://refactoring.guru/es/smells/message-chains)
    

## Credits

* [Code Smell 10 - Too Many Arguments](https://maximilianocontieri.com/code-smell-10-too-many-arguments) by @[Maxi Contieri⭐⭐⭐](@mcsee)
