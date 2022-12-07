# How to inject multiple implementations in Spring Framework

Lately, I talked with one of my collages during code review. We've discussed one of Spring's (along with other frameworks like Micronaut) hidden gems. This feature could simplify their code.

Let's assume our task is to develop a good morning greeting system. We need to support the languages English and Hebrew. The system will greet us in each of the languages.

We'll start with the native implementation. We will start by introducing our greeters:

```Kotlin
class HebrewGreeter {
    fun sayGoodMorning() {
        println("בוקר טוב")
    }
}

class EnglishGreeter {
    fun sayGoodMorning() {
        println("Good morning")
    }
}
```

Now we will create a class that will hold the greeters, and call them:

```Kotlin
@Service
class GreeterService(
    private val hebrewGreeter: HebrewGreeter = HebrewGreeter(),
    private val englishGreeter: EnglishGreeter = EnglishGreeter()
) {
    fun greetInAllLanguages() {
        hebrewGreeter.sayGoodMorning()
        englishGreeter.sayGoodMorning()
    }
}
```

It all works as expected. Yet, let's assume that now we want to introduce a new greeter (for example German greeter). We will have to go and change the `GreeterService`. This is a violation of the [open–closed principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) from the SOLID principles.

To resolve the issue, we will introduce an `interface`. This `interface` will have common functionality of greeters:

```Kotlin
interface Greeter {
    fun sayGoodMorning()
}

@Component
class HebrewGreeter : Greeter {
    override fun sayGoodMorning() {
        println("בוקר טוב")
    }
}

@Component
class EnglishGreeter  : Greeter{
    override fun sayGoodMorning() {
        println("Good morning")
    }
}

@Component
class GermanGreeter : Greeter {
    override fun sayGoodMorning() {
        println("Guten Morgen")
    }
}
```

Now comes the good part. Since we implement an interface, `GreetService` can ask Spring to inject all available implementations into the constructor:

```Kotlin
@Service
class GreeterService(private val greeters: List<Greeter>) {
    fun greetInAllLanguages() {
        greeters.forEach { it.sayGoodMorning() }
    }
}
```

Our code is much simpler now. But that's not the only benefit. If we will introduce a new greeter in the future, it will be automatically added to the system!

An alternative for the `List<Greeter>` would be a `Map<String, Greeter>`. In such a case, Spring will inject a map between the full class name and the bean. For example `com.example.greeter.EnglishGreeter`.