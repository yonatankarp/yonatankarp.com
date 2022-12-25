# Kotlin Code Smell 015 - Helper Classes

> ***TL;DR:*** *Helpers don't help. They are a non-cohesive bunch of messy subroutines.*

## **Problems**

* Readability
    
* The Least surprise principle
    
* [**Bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) Fault
    
* Static methods
    

## **Solutions**

* Find a suitable name.
    
* If the helper is a library, break all the services into different methods.
    
* Methods should always be fulfilled by objects. [**Static methods**](https://yonatankarp.com/kotlin-smell-013-companion-object-functions) are another code smell.
    
* Avoid extracting the helpers to [**Anonymous Functions**](https://yonatankarp.com/kotlin-code-smells-014-anonymous-functions-abusers).
    

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

Notice *static* methods usage:

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

Alternatively, we can make the former *Helper* stateless for reuse...

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

This is a well-established cultural name and a legacy habit from structured programming.

Most developers are reluctant to let old habits go.

We must be aware of the damage these kinds of names are bringing us.

## Credits

* [Code Smell 22](https://maximilianocontieri.com/code-smell-22-helpers) - Helpers by @[Maxi Contieri](@mcsee)