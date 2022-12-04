# How to inject multiple implementations in Spring Framework

Lately, I talked with one of my collages during code review and we've discussed one of Spring (along with other frameworks like Micronaut) hidden gems that could simplify their code.

Let's assume that our task is to develop a good morning greeting system and we need to support the languages English and Hebrew, and then be greeted by each language.

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

It all works as expected. However, let's assume that now we want to introduce a new greeter (for example German greeter). We will have to go and change the `GreeterService` which is a violation of the [open–closed principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) from the SOLID principles.

To resolve the issue, we will start by introducing an `interface` that will have common functionality between greeters as follow:

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

Now coming the code part, since we're implementing an interface, in the `GreetService` we can ask Spring to inject all available implementations into the constructor:

```Kotlin
@Service
class GreeterService(private val greeters: List<Greeter>) {
    fun greetInAllLanguages() {
        greeters.forEach { it.sayGoodMorning() }
    }
}
```

Not only that the code was simplified, if we will add a new greeter in the future, but it will also be automatically added here!

An alternative for the `List<Greeter>` would be a `Map<String, Greeter>` - in such case, Spring will inject a map between the full class name (e.g. `com.example.greeter.EnglishGreeter`), and the bean.