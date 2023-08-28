---
title: "Advent of Code 2022 (Day 11) - Kotlin Edition"
subtitle: ""
slug: advent-of-code-2022-day-11-kotlin-edition
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: adventofcode2022, kotlin 
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: 
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: true
seoTitle: ""
seoDescription: ""
seriesSlug: advent-of-code
# Table of contents
enableToc: true
saveAsDraft: false
---

## Part 1

### Task

> As you finally start making your way upriver, you realize your pack is much lighter than you remember. Just then, one of the items from your pack goes flying overhead. Monkeys are playing [Keep Away](https://en.wikipedia.org/wiki/Keep_away) with your missing things!
> 
> To get your stuff back, you need to be able to predict where the monkeys will throw your items. After some careful observation, you realize the monkeys operate based on *how worried you are about each item*.
> 
> You take some notes (your puzzle input) on the items each monkey currently has, how worried you are about those items, and how the monkey makes decisions based on your worry level. For example:
> 
> ```kotlin
> Monkey 0:
>   Starting items: 79, 98
>   Operation: new = old * 19
>   Test: divisible by 23
>     If true: throw to monkey 2
>     If false: throw to monkey 3
> 
> Monkey 1:
>   Starting items: 54, 65, 75, 74
>   Operation: new = old + 6
>   Test: divisible by 19
>     If true: throw to monkey 2
>     If false: throw to monkey 0
> 
> Monkey 2:
>   Starting items: 79, 60, 97
>   Operation: new = old * old
>   Test: divisible by 13
>     If true: throw to monkey 1
>     If false: throw to monkey 3
> 
> Monkey 3:
>   Starting items: 74
>   Operation: new = old + 3
>   Test: divisible by 17
>     If true: throw to monkey 0
>     If false: throw to monkey 1
> ```
> 
> Each monkey has several attributes:
> 
> * `Starting items` lists your *worry level* for each item the monkey is currently holding in the order they will be inspected.
>     
> * `Operation` shows how your worry level changes as that monkey inspects an item. (An operation like `new = old * 5` means that your worry level after the monkey inspected the item is five times whatever your worry level was before inspection.)
>     
> * `Test` shows how the monkey uses your worry level to decide where to throw an item next.
>     
>     * `If true` shows what happens with an item if the `Test` was true.
>         
>     * `If false` shows what happens with an item if the `Test` was false.
>         
> 
> After each monkey inspects an item but before it tests your worry level, your relief that the monkey's inspection didn't damage the item causes your worry level to be *divided by three* and rounded down to the nearest integer.
> 
> The monkeys take turns inspecting and throwing items. On a single monkey's *turn*, it inspects and throws all of the items it is holding one at a time and in the order listed. Monkey `0` goes first, then monkey `1`, and so on until each monkey has had one turn. The process of each monkey taking a single turn is called a *round*.
> 
> When a monkey throws an item to another monkey, the item goes on the *end* of the recipient monkey's list. A monkey that starts a round with no items could end up inspecting and throwing many items by the time its turn comes around. If a monkey is holding no items at the start of its turn, its turn ends.
> 
> In the above example, the first round proceeds as follows:
> 
> ```kotlin
> Monkey 0:
>   Monkey inspects an item with a worry level of 79.
>     Worry level is multiplied by 19 to 1501.
>     Monkey gets bored with item. Worry level is divided by 3 to 500.
>     Current worry level is not divisible by 23.
>     Item with worry level 500 is thrown to monkey 3.
>   Monkey inspects an item with a worry level of 98.
>     Worry level is multiplied by 19 to 1862.
>     Monkey gets bored with item. Worry level is divided by 3 to 620.
>     Current worry level is not divisible by 23.
>     Item with worry level 620 is thrown to monkey 3.
> Monkey 1:
>   Monkey inspects an item with a worry level of 54.
>     Worry level increases by 6 to 60.
>     Monkey gets bored with item. Worry level is divided by 3 to 20.
>     Current worry level is not divisible by 19.
>     Item with worry level 20 is thrown to monkey 0.
>   Monkey inspects an item with a worry level of 65.
>     Worry level increases by 6 to 71.
>     Monkey gets bored with item. Worry level is divided by 3 to 23.
>     Current worry level is not divisible by 19.
>     Item with worry level 23 is thrown to monkey 0.
>   Monkey inspects an item with a worry level of 75.
>     Worry level increases by 6 to 81.
>     Monkey gets bored with item. Worry level is divided by 3 to 27.
>     Current worry level is not divisible by 19.
>     Item with worry level 27 is thrown to monkey 0.
>   Monkey inspects an item with a worry level of 74.
>     Worry level increases by 6 to 80.
>     Monkey gets bored with item. Worry level is divided by 3 to 26.
>     Current worry level is not divisible by 19.
>     Item with worry level 26 is thrown to monkey 0.
> Monkey 2:
>   Monkey inspects an item with a worry level of 79.
>     Worry level is multiplied by itself to 6241.
>     Monkey gets bored with item. Worry level is divided by 3 to 2080.
>     Current worry level is divisible by 13.
>     Item with worry level 2080 is thrown to monkey 1.
>   Monkey inspects an item with a worry level of 60.
>     Worry level is multiplied by itself to 3600.
>     Monkey gets bored with item. Worry level is divided by 3 to 1200.
>     Current worry level is not divisible by 13.
>     Item with worry level 1200 is thrown to monkey 3.
>   Monkey inspects an item with a worry level of 97.
>     Worry level is multiplied by itself to 9409.
>     Monkey gets bored with item. Worry level is divided by 3 to 3136.
>     Current worry level is not divisible by 13.
>     Item with worry level 3136 is thrown to monkey 3.
> Monkey 3:
>   Monkey inspects an item with a worry level of 74.
>     Worry level increases by 3 to 77.
>     Monkey gets bored with item. Worry level is divided by 3 to 25.
>     Current worry level is not divisible by 17.
>     Item with worry level 25 is thrown to monkey 1.
>   Monkey inspects an item with a worry level of 500.
>     Worry level increases by 3 to 503.
>     Monkey gets bored with item. Worry level is divided by 3 to 167.
>     Current worry level is not divisible by 17.
>     Item with worry level 167 is thrown to monkey 1.
>   Monkey inspects an item with a worry level of 620.
>     Worry level increases by 3 to 623.
>     Monkey gets bored with item. Worry level is divided by 3 to 207.
>     Current worry level is not divisible by 17.
>     Item with worry level 207 is thrown to monkey 1.
>   Monkey inspects an item with a worry level of 1200.
>     Worry level increases by 3 to 1203.
>     Monkey gets bored with item. Worry level is divided by 3 to 401.
>     Current worry level is not divisible by 17.
>     Item with worry level 401 is thrown to monkey 1.
>   Monkey inspects an item with a worry level of 3136.
>     Worry level increases by 3 to 3139.
>     Monkey gets bored with item. Worry level is divided by 3 to 1046.
>     Current worry level is not divisible by 17.
>     Item with worry level 1046 is thrown to monkey 1.
> ```
> 
> After round 1, the monkeys are holding items with these worry levels:
> 
> ```kotlin
> Monkey 0: 20, 23, 27, 26
> Monkey 1: 2080, 25, 167, 207, 401, 1046
> Monkey 2: 
> Monkey 3: 
> ```
> 
> Monkeys 2 and 3 aren't holding any items at the end of the round; they both inspected items during the round and threw them all before the round ended.
> 
> This process continues for a few more rounds:
> 
> ```kotlin
> After round 2, the monkeys are holding items with these worry levels:
> Monkey 0: 695, 10, 71, 135, 350
> Monkey 1: 43, 49, 58, 55, 362
> Monkey 2: 
> Monkey 3: 
> 
> After round 3, the monkeys are holding items with these worry levels:
> Monkey 0: 16, 18, 21, 20, 122
> Monkey 1: 1468, 22, 150, 286, 739
> Monkey 2: 
> Monkey 3: 
> 
> After round 4, the monkeys are holding items with these worry levels:
> Monkey 0: 491, 9, 52, 97, 248, 34
> Monkey 1: 39, 45, 43, 258
> Monkey 2: 
> Monkey 3: 
> 
> After round 5, the monkeys are holding items with these worry levels:
> Monkey 0: 15, 17, 16, 88, 1037
> Monkey 1: 20, 110, 205, 524, 72
> Monkey 2: 
> Monkey 3: 
> 
> After round 6, the monkeys are holding items with these worry levels:
> Monkey 0: 8, 70, 176, 26, 34
> Monkey 1: 481, 32, 36, 186, 2190
> Monkey 2: 
> Monkey 3: 
> 
> After round 7, the monkeys are holding items with these worry levels:
> Monkey 0: 162, 12, 14, 64, 732, 17
> Monkey 1: 148, 372, 55, 72
> Monkey 2: 
> Monkey 3: 
> 
> After round 8, the monkeys are holding items with these worry levels:
> Monkey 0: 51, 126, 20, 26, 136
> Monkey 1: 343, 26, 30, 1546, 36
> Monkey 2: 
> Monkey 3: 
> 
> After round 9, the monkeys are holding items with these worry levels:
> Monkey 0: 116, 10, 12, 517, 14
> Monkey 1: 108, 267, 43, 55, 288
> Monkey 2: 
> Monkey 3: 
> 
> After round 10, the monkeys are holding items with these worry levels:
> Monkey 0: 91, 16, 20, 98
> Monkey 1: 481, 245, 22, 26, 1092, 30
> Monkey 2: 
> Monkey 3: 
> 
> ...
> 
> After round 15, the monkeys are holding items with these worry levels:
> Monkey 0: 83, 44, 8, 184, 9, 20, 26, 102
> Monkey 1: 110, 36
> Monkey 2: 
> Monkey 3: 
> 
> ...
> 
> After round 20, the monkeys are holding items with these worry levels:
> Monkey 0: 10, 12, 14, 26, 34
> Monkey 1: 245, 93, 53, 199, 115
> Monkey 2: 
> Monkey 3: 
> ```
> 
> Chasing all of the monkeys at once is impossible; you're going to have to focus on the *two most active* monkeys if you want any hope of getting your stuff back. Count the *total number of times each monkey inspects items* over 20 rounds:
> 
> ```kotlin
> Monkey 0 inspected items 101 times.
> Monkey 1 inspected items 95 times.
> Monkey 2 inspected items 7 times.
> Monkey 3 inspected items 105 times.
> ```
> 
> In this example, the two most active monkeys inspected items 101 and 105 times. The level of *monkey business* in this situation can be found by multiplying these together: `10605`.
> 
> Figure out which monkeys to chase by counting how many items they inspect over 20 rounds. *What is the level of monkey business after 20 rounds of stuff-slinging simian shenanigans?*

### Solution

* We will use regex to parse our input into a `Monkey` class that would hold all the required information for us.
    
* Each monkey in the list will continue to interact with the items until all items are passed to a different monkey.
    
* Calculate the `monkey business` score. The score is based on the top 2 most active monkeys.
    

Let's start with our `Monkey` class. For clarity, I defined 2 new type aliases in the code:

```kotlin
typealias MonkeyId = Int
typealias WorryLevel = Long
```

Our class will contain the following properties:

* `id` - the monkey id (e.g. `0`, `1`)
    
* `items` - a mutable list of integers that represents the items the monkey holds
    
* `operation` - a pair of `String` to `String` where the 1st string is the operation type (e.g. `+`, `*`) and the 2nd is the value (e.g. `16`, `21`, or `old`)
    
* `divider` - the divider that is used for the monkey test on which monkey to pass the item.
    
* `trueId` - the monkey id to use if the test was successful.
    
* `falseId` - the monkey id to use if the test failed.
    
* `numberOfInteractions` - a read-only property. It would tell us how many interactions each monkey had with items.
    

The base class would look like this:

```kotlin
private data class Monkey(
    val id: MonkeyId,
    val items: MutableList<WorryLevel>,
    val operation: Pair<String, String>,
    val divider: Int,
    val trueId: MonkeyId,
    val falseId: MonkeyId
) {
    var numberOfInteractions: Int = 0
        private set
}
```

**Note:** We're using `Long` and not `Int` for the `WorryLevel` level as with `Int` we will reach [integer overflow](https://en.wikipedia.org/wiki/Integer_overflow) during the calculation, and our result will be wrong.

Next, we will define the method that takes the input and parse it into a `Monkey` object. Note that we will split our input by empty lines (`\n\n`) so each input we get is a block of 6 lines.

Let's 1st look at the regex we will use and explain it:

```kotlin
private val regex = """
    Monkey (\d+):
      Starting items: ((\d+,? ?)+)
      Operation: new = old (.) (\w+)
      Test: divisible by (\d+)
        If true: throw to monkey (\d+)
        If false: throw to monkey (\d+)
""".trimIndent().toRegex()
```

We will analyze it line by line:

* **Group 1:** `Monkey (\d+):` - this line will catch the `id` of the monkey that is 1 or more digits (`0-9`).
    
* **Groups 2 and 3:** `Starting items: ((\d+,? ?)+)` - This part will catch 1 or more items that are separated by `,` . Note that this will catch all the items as a single string (e.g. `79, 98`). Note that group 3 will hold only the latest item in the list, and thus we will use only group 2.
    
* **Groups 4 and 5:** `Operation: new = old (.) (\w+)` - This part will catch the operation to use (e.g. `+`, `*`) as a single character, and the value to add as a word (`a-z`, `A-Z` and `0-9` characters). The reason behind it is that the value can either be a number or the word `old`.
    
* **Group 6:** `Test: divisible by (\d+)` - This part catches the test divider as a number of 1 or more digits (e.g. `21`).
    
* **Group 7:** `If true: throw to monkey (\d+)` - This part catches the id of the monkey to pass items to if the test was successful. The id is of 1 or more digits.
    
* **Group 8:** `If false: throw to monkey (\d+)` - This part catches the id of the monkey to pass items to if the test failed. The id is of 1 or more digits.
    

We can now build our parsing function on the `Monkey`'s companion object:

```kotlin
private class Monkey(
   // Same code as before
) {
    companion object {
        private val regex = """
            Monkey (\d+):
              Starting items: ((\d+,? ?)+)
              Operation: new = old (.) (\w+)
              Test: divisible by (\d+)
                If true: throw to monkey (\d+)
                If false: throw to monkey (\d+)
        """.trimIndent().toRegex()

        fun parse(input: String) =
            regex
                .find(input)!!
                .groupValues
                .let { groups ->
                    Monkey(
                        id = groups[1].toInt(),
                        items = groups[2].split(", ").map { it.toLong() }.toMutableList(),
                        operation = Pair(groups[4], groups[5]),
                        divider = groups[6].toInt(),
                        trueId = groups[7].toInt(),
                        falseId = groups[8].toInt()
                    )
                }
    }
}
```

Now we can finally parse our input into a map of monkeys. The map will be between the monkey id and the `Monkey` object:

```kotlin
private val monkeys = input
    .filter { it.isNotBlank() }
    .associate {
        val monkey = Monkey.parse(it)
        monkey.id to monkey
    }
```

The next step is to define the function that makes the monkey interact with the 1st item in the list. The order of actions in the function would be as follow:

* Increase the number of interactions for this monkey by 1.
    
* Remove the 1st item from the monkey's list.
    
* Based on the value:
    
    * if the value is `old` - set the value to `item`.
        
    * else parse the value into an integer.
        
* Calculate the new value based on the operation type.
    
* Apply the relief operation (divide by 3) to the new worry level.
    
* Based on the divider decide if we pass the item to `trueId` or `falseId`
    
* Return a pair of `MonkeyId` to `WorryLevel`
    

```kotlin
private class Monkey(
 /// ...
) {
   // ... All previous code remains the same

    fun interact(reliefOp: (Long) -> Long): Pair<MonkeyId, WorryLevel> {
        numberOfInteractions++

        val item = items.removeFirst()
        val value =
            if (operation.second == "old") item
            else operation.second.toLong()

        return when (operation.first) {
            "+" -> item + value
            "*" -> item * value
            else -> throw IllegalArgumentException("Unknown operation: ${operation.first}")
        }.let {
            val worry = reliefOp(it)

            val targetId = when {
                worry % divider == 0L -> trueId
                else -> falseId
            }

            targetId to worry
        }
    }
}
```

The next step would be creating a function that can simulate the game the monkeys are playing. The function would receive 2 parameters. The number of rounds to perform, and a function that is executed after each time a monkey interacts with an item. The method would be used to handle our relief factor (divide by 3). The method would iterate over all the monkeys. For each one, it would transfer its items to the correct monkey based on the results of the `interact` method.

```kotlin
private fun simulate(rounds: Int, reliefOp: (Long) -> Long) =
    repeat(rounds) {
        monkeys.values.forEach { monkey ->
            while (monkey.items.isNotEmpty()) {
                val (toId, item) = monkey.interact(reliefOp)
                monkeys[toId]!!.items.add(item)
            }
        }
    }
```

Next, we need to calculate the monkey business based on the rounds the monkey played. The method would do the following:

* Sort (desc) the monkey objects by the number of interactions
    
* Take the top 2 monkeys
    
* Multiply the results using the `fold` method.
    

```kotlin
private fun calculateMonkeyBusiness() =
    monkeys
        .values
        .sortedByDescending { it.numberOfInteractions }
        .take(2)
        .fold(1L) { acc, monkey -> acc * monkey.numberOfInteractions }
```

Now we have everything that we need to solve part 1:

```kotlin
fun solvePart1(): Long {
    simulate(20) { it / 3 }
    return calculateMonkeyBusiness()
}
```

Let's finish by running our test cases:

```kotlin
@Test
fun `Part 1 - Example`() {
    val day11 = Day11(exampleInput)
    assertEquals(10605L, day11.solvePart1())
}

@Test
fun `Part 1 - Real Input`() {
    val day11 = Day11(resourceSplitOnBlankLines("2022/day11.txt"))
    assertEquals(110220L, day11.solvePart1())
}
```

## Part 2

### Task

> You're worried you might not ever get your items back. So worried, in fact, that your relief that a monkey's inspection didn't damage an item *no longer causes your worry level to be divided by three*.
> 
> Unfortunately, that relief was all that was keeping your worry levels from reaching *ridiculous levels*. You'll need to *find another way to keep your worry levels manageable*.
> 
> At this rate, you might be putting up with these monkeys for a *very long time* - possibly `10000` rounds!
> 
> With these new rules, you can still figure out the monkey business after 10000 rounds. Using the same example above:
> 
> ```kotlin
> == After round 1 ==
> Monkey 0 inspected items 2 times.
> Monkey 1 inspected items 4 times.
> Monkey 2 inspected items 3 times.
> Monkey 3 inspected items 6 times.
> 
> == After round 20 ==
> Monkey 0 inspected items 99 times.
> Monkey 1 inspected items 97 times.
> Monkey 2 inspected items 8 times.
> Monkey 3 inspected items 103 times.
> 
> == After round 1000 ==
> Monkey 0 inspected items 5204 times.
> Monkey 1 inspected items 4792 times.
> Monkey 2 inspected items 199 times.
> Monkey 3 inspected items 5192 times.
> 
> == After round 2000 ==
> Monkey 0 inspected items 10419 times.
> Monkey 1 inspected items 9577 times.
> Monkey 2 inspected items 392 times.
> Monkey 3 inspected items 10391 times.
> 
> == After round 3000 ==
> Monkey 0 inspected items 15638 times.
> Monkey 1 inspected items 14358 times.
> Monkey 2 inspected items 587 times.
> Monkey 3 inspected items 15593 times.
> 
> == After round 4000 ==
> Monkey 0 inspected items 20858 times.
> Monkey 1 inspected items 19138 times.
> Monkey 2 inspected items 780 times.
> Monkey 3 inspected items 20797 times.
> 
> == After round 5000 ==
> Monkey 0 inspected items 26075 times.
> Monkey 1 inspected items 23921 times.
> Monkey 2 inspected items 974 times.
> Monkey 3 inspected items 26000 times.
> 
> == After round 6000 ==
> Monkey 0 inspected items 31294 times.
> Monkey 1 inspected items 28702 times.
> Monkey 2 inspected items 1165 times.
> Monkey 3 inspected items 31204 times.
> 
> == After round 7000 ==
> Monkey 0 inspected items 36508 times.
> Monkey 1 inspected items 33488 times.
> Monkey 2 inspected items 1360 times.
> Monkey 3 inspected items 36400 times.
> 
> == After round 8000 ==
> Monkey 0 inspected items 41728 times.
> Monkey 1 inspected items 38268 times.
> Monkey 2 inspected items 1553 times.
> Monkey 3 inspected items 41606 times.
> 
> == After round 9000 ==
> Monkey 0 inspected items 46945 times.
> Monkey 1 inspected items 43051 times.
> Monkey 2 inspected items 1746 times.
> Monkey 3 inspected items 46807 times.
> 
> == After round 10000 ==
> Monkey 0 inspected items 52166 times.
> Monkey 1 inspected items 47830 times.
> Monkey 2 inspected items 1938 times.
> Monkey 3 inspected items 52013 times.
> ```
> 
> After 10000 rounds, the two most active monkeys inspected items 52166 and 52013 times. Multiplying these together, the level of *monkey business* in this situation is now `2713310158`.
> 
> Worry levels are no longer divided by three after each item is inspected; you'll need to find another way to keep your worry levels manageable. Starting again from the initial state in your puzzle input, *what is the level of monkey business after 10000 rounds?*

### Solution

This part took me longer to figure out. since we're doing `10,000` iterations here, the numbers are growing very fast. As a result, I reached [integer overflow](https://en.wikipedia.org/wiki/Integer_overflow) for `Long` as well. My 1st intuition was use `BigInteger`, but the code execution took forever. After some searching online, I've found the perfect solution. As all the dividers are prime numbers we can use the [Lowest common denominator](https://en.wikipedia.org/wiki/Lowest_common_denominator#:~:text=The%20expression%20%22lowest%20common%20denominator,largest%20possible%20number%20of%20people.) (LCD) algorithm. We can use the LCD value as a modulo for our worry level and ensure that it remains within the [Long value range](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-long/).

Hene, the only change required from part 1 is changing the relief operation to a modulo of the LCD value.

```kotlin
    fun solvePart2(): Long {
        // all the divisors used for tests are primes. Therefore,
        // we can use the LCM (least common denominator) as the
        // modulo for the numbers, so they won't explode before
        // round 10k
        val lcd = monkeys
            .map { it.value.divider }
            .reduce(Int::times)
        simulate(10_000) { it % lcd }
        return calculateMonkeyBusiness()
    }
```

We can now run our test cases and ensure everything works:

```kotlin
@Test
fun `Part 2 - Example`() {
    val day11 = Day11(exampleInput)
    assertEquals(2713310158L, day11.solvePart2())
}

@Test
fun `Part 2 - Real Input`() {
    val day11 = Day11(resourceSplitOnBlankLines("2022/day11.txt"))
    assertEquals(19457438264L, day11.solvePart2())
}
```

All of the code that was presented in the article, along with the utility classes that are used are available in [**my GitHub account.**](https://github.com/yonatankarp/advent-of-code)

See you on the next challenge!

## Links

* The code of this post is available [**here**](https://github.com/yonatankarp/advent-of-code/commit/f8f2fd3f90cdc0c3f68e2d2b07e989dbbc5bbb3f).
    

## More Info

* [Kotlin Regex](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-regex/)
    
* [Regex in Java](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html)

%%[contact-me]
