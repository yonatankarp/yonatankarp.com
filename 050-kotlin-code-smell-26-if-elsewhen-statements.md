---
title: "Kotlin Code Smell 26 - if-else/when statements"
subtitle: "Code Evolution: Conquering Complexity with Patterns"
slug: kotlin-code-smell-26-if-elsewhen-statements
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Zkx_DgMQink/upload/3ba7b97b2b1663ce268d1032152b7e74.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Best Practices: Avoiding Code Smells with if-else/when Statemen"
seoDescription: "Refine Kotlin code by eliminating if-else/when code smells using design patterns for efficient, maintainable programming."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

*First programming lesson: Control structures. Senior developer lesson: avoid them.*

## Problems

* Too many decisions together
* Coupling
* Duplicated code
* Violation of [**Open/Closed Principle**](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).
* A new condition should not change the main algorithm.
* Nulls

## Solutions

1. Polymorphism
2. Create hierarchies/compose objects following the *Open closed principle*.
3. Use [**State pattern**](https://en.wikipedia.org/wiki/State_pattern) to model transitions.
4. Use [**Strategy Pattern**](https://en.wikipedia.org/wiki/Strategy_pattern)/[**Method Object**](https://wiki.c2.com/?MethodObject) to choose for branches.

## Examples

* Discrete Values
* State transition
* Algorithm choice.

## Sample Code

### Wrong

```kotlin
fun convertToMp3(source: Path, mimeType: String) =
    when(mimeType) {
        "audio/mpeg" -> convertMpegToMp3(source)
        "audio/wav" -> convertWavToMp3(source)
        "audio/ogg" -> convertOggToMp3(source)
        // Lots of new if-else cases
        else -> throw IllegalArgumentException("Unknown mime type")
    }

fun convertMpegToMp3(source: Path) = println("Convert from mpeg")
fun convertWavToMp3(source: Path) = println("Convert from wav")
fun convertOggToMp3(source: Path) = println("Convert from ogg")
```

### Right

```kotlin
val registeredConverters = mapOf(
    "audio/mpeg" to ::convertMpegToMp3,
    "audio/wav" to ::convertWavToMp3,
    "audio/ogg" to ::convertOggToMp3,
    // Lots of other converters
)

fun convertToMp3(source: Path, mimeType: String) =
    registeredConverters[mimeType]
        ?.let { converter -> converter(source) }
        ?: throw IllegalArgumentException("No converter found")

fun convertMpegToMp3(source: Path) = println("Convert from mpeg")
fun convertWavToMp3(source: Path) = println("Convert from wav")
fun convertOggToMp3(source: Path) = println("Convert from ogg")
```

After Further Refactoring (Using Objects)

```kotlin
interface Mp3Converter {
    fun convertToMp3(source: Path)
}

class ConvertMpegToMp3 : Mp3Converter {
    override fun convertToMp3(source: Path) =
        println("Convert from mpeg")
}

class ConvertWavToMp3 : Mp3Converter {
    override fun convertToMp3(source: Path) =
        println("Convert from wav")
}

class ConvertOggToMp3 : Mp3Converter {
    override fun convertToMp3(source: Path) =
        println("Convert from ogg")
}

// Many more converters

val registeredConverters = mapOf(
    "audio/mpeg" to ConvertMpegToMp3(),
    "audio/wav" to ConvertWavToMp3(),
    "audio/ogg" to ConvertOggToMp3(),
    // Lots of other converters
)

fun convertToMp3(source: Path, mimeType: String) =
    registeredConverters[mimeType]
        ?.convertToMp3(source)
        ?: throw IllegalArgumentException("No converter found")
```

## Conclusion

Excessive use of if-else/when statements in Kotlin can lead to code smells, making the code difficult to maintain and understand. To avoid these issues, it is essential to utilize design patterns such as polymorphism, state patterns, and strategy patterns.

You can almost always replace the if-else/when statement with a map implementation.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 36 - Switch/case/elseif/else/if statements](https://maximilianocontieri.com/code-smell-36-switchcaseelseifelseif-statements) by @[Maximiliano Contieri](@mcsee)
