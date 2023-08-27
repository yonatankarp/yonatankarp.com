---
title: "Kotlin Code Smell 15 - Helper Classes"
seoTitle: "Avoid Code Smell: Refactor Helper Classes for Better Code Quality"
seoDescription: "Learn how to improve code quality by refactoring helper classes. Discover why static methods and anonymous functions are code smells in Kotlin."
datePublished: Wed Dec 21 2022 08:00:45 GMT+0000 (Coordinated Universal Time)
cuid: clbxd818b00j4qonveyffcm79
slug: kotlin-code-smell-15-helper-classes
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670916375024/94ycSUHnD.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

---


> **TL;DR:** Helpers are non-cohesive and messy subroutines that don't actually help.

## **Problems**

* Lack of readability
    
* Violation of the Least Surprise Principle
    
* Issue with **Bijection**
    
* Usage of static methods
    

## **Solutions**

* Choose an appropriate name.
    
* If the helper is a library, divide all the services into different methods.
    
* Methods should always be associated with objects. Avoid using **static methods**, as they are also considered a code smell.
    
* Refrain from extracting helpers into **Anonymous Functions**.
    

## **Sample Code**

### Wrong

```kotlin
object UserHelper {
    fun getFullName(user: User) =
        "${user.firstName} ${user.lastName}"

    fun getCategory(user: User) =
        if (user.points > 70) 'A' else 'B'
}
```

Note the use of *static* methods:

```kotlin
data class User(
    val firstName: String,
    val lastName: String,
    val points: Int
)

fun main() {
    val alice = User(
        firstName = "Alice",
        lastName = "Gray",
        points = 78
    )

    val fullName = UserHelper.getFullName(alice)
    val category = UserHelper.getCategory(alice)
}
```

### Right

```kotlin
class FullNameFormatter(private val user: User) {
    val fullName: String
        get() = "${user.firstName} ${user.lastName}"
}

class CategoryCalculator(private val user: User) {
    val displayName: Char
        get() = if(user.points > 70) 'A' else 'B'
}

fun main() {
    val alice = User(
        firstName = "Alice",
        lastName = "Gray",
        points = 78
    )

    val fullName = FullNameFormatter(alice).fullName
    val category = CategoryCalculator(alice).displayName
}
```

Alternatively, the former *Helper* can be made stateless for reuse...

```kotlin
class FullNameFormatter {
    fun fullName(user: User): String =
        "${user.firstName} ${user.lastName}"
}

class CategoryCalculator {
    fun displayName(user: User): Char =
        if (user.points > 70) 'A' else 'B'
}

fun main() {
    val alice = User(
        firstName = "Alice",
        lastName = "Gray",
        points = 78
    )

    val fullName = FullNameFormatter().fullName(alice)
    val category = CategoryCalculator().displayName(alice)
}
```

## **Conclusion**

The usage of helper classes is a well-established cultural practice and a legacy habit from structured programming.

However, these names are causing harm and should be reconsidered.

It is essential for developers to let go of old habits and be aware of the negative impact these names can have.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 22](https://maximilianocontieri.com/code-smell-22-helpers) - Helpers by @[Maxi Contieri](@mcsee)
