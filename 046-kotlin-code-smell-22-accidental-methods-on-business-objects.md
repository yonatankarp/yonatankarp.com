---
title: "Kotlin Code Smell 22 - Accidental Methods on Business Objects"
subtitle: "Bloated Cars & Code: When Your Objects Carry Too Much Junk in the Trunk"
slug: kotlin-code-smell-22-accidental-methods-on-business-objects
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/XTZHvQ9MUKI/upload/8f397712cf318923e98a36e9d5fde76b.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Clean Code: Avoiding Accidental Methods on Business Objects"
seoDescription: "Learn how to improve code quality by separating unrelated concerns from business objects. Prevent coupling and maintain readability."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


Adding code for persistence, serialization, displaying, importing, and exporting to an object can lead to bloating its protocol and create unnecessary coupling.

## Problem

* Readability
    
* Coupling
    
* Maintainability
    

## Solution

1. Keep your objects clean by focusing on their core responsibilities.
    
2. Decouple business objects from unrelated concerns.
    
3. Separate accidental concerns: Move functionality related to a specific dedicated objects (e.g. Persistence, Formatting, Serialization).
    
4. Keep the essential protocol of your objects concise and focused.
    

## Examples

* Persistence
    
* Identifiers
    
* Serialization
    
* Formatting
    

## Sample Code

### Wrong

```kotlin
class Car(
    private val company: Company,
    private val color: Color,
    private val engine: Engine
) {
    fun goTo(coordinate: Coordinate) {
        move(coordinate)
    }

    fun startEngine() {
        engine.start()
    }

    fun display() {
        println("This is a $color $company")
    }

    fun toJson(): String {
        return "json"
    }

    fun updateOnDatabase() {
        database.update(this)
    }

    fun getId(): Int {
        return id
    }

    fun fromRow(row: Row) {
        database.convertFromRow(row, this)
    }

    fun forkCar() {
        // Concurrency is accidental
        ConcurrencySemaphoreSingleton.getInstance().forkCar(this)
    }
    
    // ...
}
```

### Right

```kotlin
class Car(
    private val company: Company,
    private val color: Color,
    private val engine: Engine
) {
    fun goTo(coordinate: Coordinate) {
        move(coordinate)
    }

    fun startEngine() {
        // Code to start the engine - all logic was extracted
        engine.start()
    }

    // ...
}
```

## Exceptions

* In some cases, certain frameworks may force us to inject code related to unrelated concerns into our objects (e.g., identifiers). In such cases, it is advisable to explore better frameworks that allow for cleaner designs.
    

## Conclusion

It is common to encounter business objects with mixed responsibilities. However, it is essential to consider the consequences and coupling that such designs can introduce.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 31 - Accidental Methods on Business Objects](https://maximilianocontieri.com/code-smell-31-accidental-methods-on-business-objects) by @[Maxi Contieri⭐⭐⭐](@mcsee)
