---
title: "Kotlin Code Smell 14 - Anonymous Functions Abusers"
subtitle: "Closures in Check: Enhancing Code Maintainability"
slug: kotlin-code-smell-14-anonymous-functions-abusers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, code-smell-1, anonymous-function
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670074998894/lYlCt6Imq.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Anonymous Function Abuse: Improve Code Quality with Encapsulated"
seoDescription: "Learn how to enhance code maintainability and reuse by encapsulating functions into objects. Avoid the pitfalls of anonymous function abuse in Kotlin." 
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Avoid excessive use of closures and functions. Encapsulate them within objects.

## **Problems**

* Maintainability
    
* Testability
    
* Code Reuse
    
* Implementation Hiding
    
* Debugging
    

## Solutions

* Wrap functions/closures
    
* Reify algorithms using a [method object](https://maximilianocontieri.com/refactoring-010-extract-method-object) / Strategy pattern
    

## Sample Code

### Wrong

```kotlin
fun sortFunction(
    list: MutableList<Int>,
    fn: (Int, Int) -> Boolean
) {
    for (i in list.indices) {
        for (j in 0 until list.size - i - 1) {
            if (fn(list[j], list[j + 1])) {
                val temp = list[j]
                list[j] = list[j + 1]
                list[j + 1] = temp
            }
        }
    }
}

fun main() {
    val scores = mutableListOf(9, 5, 2, 7, 23, 1, 3)
    sortFunction(scores) { a, b -> a > b }
}
```

### Right

```kotlin
class ElementComparator {
    fun greaterThen(firstElement: Int, secondElement: Int) =
        firstElement > secondElement

    // This is just an example. With more complex objects,
    // this comparison might not be so trivial...
}

class BubbleSortStrategy(
    private val elements: MutableList<Int>,
    private val comparator: ElementComparator
) {
    // We have a strategy; we can unit test it, change it to a
    // polymorphic implementation, or benchmark different algorithms, etc.
    fun sort() {
        for (i in elements.indices) {
            for (j in 0 until elements.size - i - 1) {
                if (comparator.greaterThen(elements[j], elements[j + 1])) {
                    swap(j)
                }
            }
        }
    }

    private fun swap(index: Int) {
        val temp = elements[index]
        elements[index] = elements[index + 1]
        elements[index + 1] = temp
    }
}

fun main() {
    val scores = mutableListOf(9, 5, 2, 7, 23, 1, 3)
    BubbleSortStrategy(scores, ElementComparator()).sort()
}
```

# Conclusion

Humans read code, and while software can handle anonymous functions, maintainability suffers when multiple closures are used. By extracting functionality into objects, we can improve code reuse and maintainability.

%%[contact-me]

# Credits

* [Code Smell 21 - Anonymous Functions Abusers](https://maximilianocontieri.com/code-smell-21-anonymous-functions-abusers) by @[Maximiliano Contieri](@mcsee)
