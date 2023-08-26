---
title: "Kotlin Code Smell 32 - Repeated Code"
seoTitle: "Eliminate Code Duplication in Kotlin: 5 Steps to Cleaner Code"
seoDescription: "Reduce code repetition in Kotlin with 5 steps for better quality, maintainability, and adherence to the DRY principle."
datePublished: Wed Aug 23 2023 08:00:09 GMT+0000 (Coordinated Universal Time)
cuid: cllng2ytb000308l399mi2jai
slug: kotlin-code-smell-32-repeated-code
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/zAZYuch7deE/upload/105e5b048e80b9419b301810cc15e656.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

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