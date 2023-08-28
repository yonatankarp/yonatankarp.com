---
title: "Kotlin Code Smell 30 - Concrete Classes Subclassified"
subtitle: "Code Chaos to Composition Brilliance: A Transformation Tale"
slug: kotlin-code-smell-30-concrete-classes-subclassified
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/GqQ1Jnl0TWQ/upload/a140e90b91e3367277d3cda82a0c44e9.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Best Practices: Avoiding Concrete Class Subclassing Pitfalls"
seoDescription: "Avoid concrete class subclassing pitfalls in Kotlin by mastering best practices, using composition, and understanding the Liskov Substitution Principle."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

## Problem

* Bad Models
* Coupling
* [**Liskov Substitution**](https://en.wikipedia.org/wiki/Liskov_substitution_principle) Violation
* Method overriding
* [**Mapper**](https://maximilianocontieri.com/what-is-wrong-with-software) fault

## Solution

1. Subclasses should be specializations.
2. Refactor Hierarchies.
3. Favor Composition.
4. Leaf classes should be concrete.
5. Not-leaf classes should be abstract.

## Sample Code

### Wrong

```kotlin
class Stack<T> : ArrayList<T>() {
    fun push(value: T) { … }
    fun pop(): T { … }
}
// Stack does not behave Like an ArrayList
// besides pop, push, top it also implements (or overrides) get, set,
// add, remove and clear stack elements can be arbitrary accessed

// both classes are concrete
```

### Right

```kotlin
abstract class Collection {
    abstract fun size(): Int
}

class Stack<out T> : Collection() {
    private val contents = ArrayList<T>()

    fun push(value: Any) { ... }

    fun pop(): Any? { ... }

    override fun size() = contents.size
}

class ArrayList : Collection() {
    override fun size(): Int { ... }
}
```

## Conclusion

Accidental sub-classification is the first obvious advantage for junior developers.

More mature ones find composition opportunities instead.

Composition is dynamic, multiple, pluggable, more testable, more maintainable, and less coupled than inheritance.

Only subclassify an entity if it follows the relationship *behaves like*.

After subclassing, the parent class should be abstract.

Java made this mistake, and they're regretting it until now. Let's do better than Java 😁

%%[contact-me]

## Credits

* [Code Smell 43 - Concrete Classes Subclassified](https://maximilianocontieri.com/code-smell-43-concrete-classes-subclassified) by @[Maximiliano Contieri](@mcsee)
