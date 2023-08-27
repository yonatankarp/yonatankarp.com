---
title: "Kotlin Code Smell 32 - Repeated Code"
subtitle: "Breaking the Chains of Duplication for Elegant and Maintainable Code"
slug: kotlin-code-smell-32-repeated-code
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/zAZYuch7deE/upload/105e5b048e80b9419b301810cc15e656.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Eliminate Code Duplication in Kotlin: 5 Steps to Cleaner Code"
seoDescription: "Reduce code repetition in Kotlin with 5 steps for better quality, maintainability, and adherence to the DRY principle."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

## Problem

* Code Duplication
    
* Maintainability
    
* [**Don't Repeat Yourself**](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
    

## Solution

1. Find repeated patterns (not repeated code).
    
2. Create an abstraction.
    
3. Parametrize abstraction calls.
    
4. Use composition and avoid inheritance.
    
5. Unit test the new abstraction.
    

## Sample Code

### Wrong

```kotlin
class WordProcessor(var text: String = "") {
    fun replaceText(patternToFind: String, textToReplace: String) {
        text = "<<< ${text.replace(patternToFind, textToReplace} >>>"
    }
}

class Obfuscator(var text: String = "") {
    fun obfuscate(patternToFind: String, textToReplace: String) {
        text = text.lowercase().replace(patternToFind, textToReplace)
    }
}
```

### Right

```kotlin
class TextReplacer {
    fun replace(
        patternToFind: String,
        textToReplace: String,
        subject: String,
        replaceFunction: (String, String, String) -> String,
        postProcessClosure: (String) -> String
    ): String =
        replaceFunction(patternToFind, textToReplace, subject)
            .let(postProcessClosure)
}

class WordProcessor(private var text: String = "") {
    fun replaceText(patternToFind: String, textToReplace: String) {
        text = TextReplacer()
            .replace(
                patternToFind,
                textToReplace,
                text,
                replaceFunction = { pattern, replace, subject ->
                    subject.replace(pattern, replace)
                },
                postProcessClosure = { "<<< $it >>>" }
            )
    }
}

class Obfuscator(private var text: String = "") {
    fun obfuscate(patternToFind: String, textToReplace: String) {
        text = TextReplacer()
            .replace(
                patternToFind,
                textToReplace,
                text,
                replaceFunction = { pattern, replace, subject ->
                    subject.replace(pattern, replace, ignoreCase = true)
                },
                postProcessClosure = { it.lowercase() }
            )
    }
}
```

## Conclusion

Repeated code is always a smell. Copying and pasting code is always a shame. With our refactoring tools, we need to accept the challenge of removing duplication and trust our tests as a safety net.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 46 - Repeated Code](https://maximilianocontieri.com/code-smell-46-repeated-code) by @[Maxi Contieri](@mcsee)
