---
title: "Kotlin Code Smell 16 - Instance Type Checking for Polymorphism"
seoTitle: "Eliminating Type Checking: Improve Polymorphism and Code Quality"
seoDescription: "Learn how to enhance code quality by replacing instance type checking with polymorphism. Eliminate coupling, improve flexibility, and simplify your codebase"
datePublished: Fri Dec 23 2022 08:30:44 GMT+0000 (Coordinated Universal Time)
cuid: clc096b5f04ugrsnv9ti07t4x
slug: kotlin-code-smell-16-instance-type-checking-for-polymorphism
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670923029137/WWJE8X1D7.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

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

## **Conclusion**

Testing for a class type [**couples**](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem) objects with [**accidental decisions**](https://maximilianocontieri.com/no-silver-bullet) and violates [**bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) since no such control exists in the real world. It is a code smell that indicates our models are not good enough.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 23 - Instance Type Checking](https://maximilianocontieri.com/code-smell-23-instance-type-checking) by @[Maxi Contieri](@mcsee)