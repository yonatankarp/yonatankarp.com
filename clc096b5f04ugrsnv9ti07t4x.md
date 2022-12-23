# Kotlin Code Smell 016 - Instance Type Checking For Polymorphism

> ***TL;DR:*** *Trust your collaborators. Don't check who they are. Ask them to do it instead.*

## **Problems**

*   [**Coupling**](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem)
    
*   Metamodel interference
    
*   [**IFs**](https://maximilianocontieri.com/how-to-get-rid-of-annoying-ifs-forever)
    

## Solutions

*   Avoid `is` , `as`, `::class`, `::class.java` , etc...
    
*   Don't use Reflection and [**Meta-programming**](https://maximilianocontieri.com/laziness-i-meta-programming) for Domain Objects.
    
*   Replace [***IFs***](https://maximilianocontieri.com/how-to-get-rid-of-annoying-ifs-forever) with polymorphism.
    
*   Use [**complete objects**](https://maximilianocontieri.com/nude-models-part-i-setters), avoid [**nulls**](https://maximilianocontieri.com/code-smell-12-null) and setters, favor [**immutability**](https://maximilianocontieri.com/the-evil-powers-of-mutants) and you will never ifs.
    

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
    when(animal) {
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

Testing for a class type [**couples**](https://maximilianocontieri.com/coupling-the-one-and-only-software-design-problem) the objects with [**accidental decisions**](https://maximilianocontieri.com/no-silver-bullet) and violates [**bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) since no such control exists in the real world. It is a smell our models are not good enough.

## Credits

*   [Code Smell 23 - Instance Type Checking](https://maximilianocontieri.com/code-smell-23-instance-type-checking) by @[Maxi Contieri](@mcsee)