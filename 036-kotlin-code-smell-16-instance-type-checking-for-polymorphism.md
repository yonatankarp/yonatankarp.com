---
title: "Kotlin Code Smell 16 - Instance Type Checking for Polymorphism"
subtitle: "Ditch the Type Checks: Let Your Code Dance to Polymorphism's Tune"
slug: kotlin-code-smell-16-instance-type-checking-for-polymorphism
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670923029137/WWJE8X1D7.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Eliminating Type Checking: Improve Polymorphism and Code Quality"
seoDescription: "Learn how to enhance code quality by replacing instance type checking with polymorphism. Eliminate coupling, improve flexibility, and simplify your codebase"
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


> ***TL;DR:*** *Trust your collaborators. Don't check who they are. Ask them to do it instead.*

## **Problems**

* [**Coupling**](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem): Objects are tightly coupled due to instance type checking.
    
* Metamodel interference: The use of instance type checking interferes with the metamodel.
    
* [**IFs**](https://maximilianocontieri.com/how-to-get-rid-of-annoying-ifs-forever): Excessive use of if statements.
    

## Solutions

* Avoid using `is`, `as`, `::class`, `::`[`class.java`](http://class.java), etc...
    
* Don't use Reflection and [**Meta-programming**](https://maximilianocontieri.com/laziness-i-meta-programming) for Domain Objects.
    
* Replace [***IFs***](https://maximilianocontieri.com/how-to-get-rid-of-annoying-ifs-forever) with polymorphism.
    
* Use [**complete objects**](https://maximilianocontieri.com/nude-models-part-i-setters), avoid [**nulls**](https://maximilianocontieri.com/code-smell-12-null), favor [**immutability**](https://maximilianocontieri.com/the-evil-powers-of-mutants), and you will never need if statements.
    

## Sample Code

### Wrong

```kotlin
class Rabbit {
    fun run() = println("I'm running! 🏃‍")
}

class Seagull {
    fun fly() = println("I'm flying! ✈️")
}

fun move(animal: Any) =
    when (animal) {
        is Rabbit -> animal.run()
        is Seagull -> animal.fly()
        else -> throw IllegalArgumentException("Unknown animal type")
    }


fun main() {
    val bunny = Rabbit()
    val livingstone = Seagull()

    move(bunny)
    move(livingstone)
}
```

### Right

```kotlin
abstract class Animal {
    abstract fun move()
}

class Rabbit : Animal() {
    override fun move() = println("I'm running! 🏃‍")
}

class Seagull : Animal() {
    override fun move() = println("I'm flying! ✈️")
}

fun main() {
    val bunny = Rabbit()
    val livingstone = Seagull()

    bunny.move()
    livingstone.move()
}
```

%%[contact-me]

## **Conclusion**

Testing for a class type [**couples**](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem) objects with [**accidental decisions**](https://maximilianocontieri.com/no-silver-bullet) and violates [**bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) since no such control exists in the real world. It is a code smell that indicates our models are not good enough.

## Credits

* [Code Smell 23 - Instance Type Checking](https://maximilianocontieri.com/code-smell-23-instance-type-checking) by @[Maximiliano Contieri](@mcsee)
