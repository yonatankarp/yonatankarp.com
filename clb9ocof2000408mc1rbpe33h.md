# Advent of Code 2022 - Day 1 - Kotlin Edition

## Day 1 - Calorie Counting

### Part 1

#### Task

> Santa's reindeer typically eat regular reindeer food, but they need a lot of magical energy to deliver presents on Christmas. For that, their favorite snack is a special type of star fruit that only grows deep in the jungle. The Elves have brought you on their annual expedition to the grove where the fruit grows.
> 
> To supply enough magical energy, the expedition needs to retrieve a minimum of fifty stars by December 25th. Although the Elves assure you that the grove has plenty of fruit, you decide to grab any fruit you see along the way, just in case.
> 
> Collect stars by solving puzzles. Two puzzles will be made available on each day in the Advent calendar; the second puzzle is unlocked when you complete the first. Each puzzle grants one star. Good luck!
> 
> The jungle must be too overgrown and difficult to navigate in vehicles or access from the air; the Elves' expedition traditionally goes on foot. As your boats approach land, the Elves begin taking inventory of their supplies. One important consideration is food - in particular, the number of Calories each Elf is carrying (your puzzle input).
> 
> The Elves take turns writing down the number of Calories contained by the various meals, snacks, rations, etc. that they've brought with them, one item per line. Each Elf separates their own inventory from the previous Elf's inventory (if any) by a blank line.
> 
> For example, suppose the Elves finish writing their items' Calories and end up with the following list:
> 
> ```text
> 1000
> 2000
> 3000
> 
> 4000
> 
> 5000
> 6000
> 
> 7000
> 8000
> 9000
> 
> 10000
> ```
> 
> This list represents the Calories of the food carried by five Elves:
> 
> *   The first Elf is carrying food with `1000`, `2000`, and `3000` Calories, a total of `6000` Calories.
>     
> *   The second Elf is carrying one food item with `4000` Calories.
>     
> *   The third Elf is carrying food with `5000` and `6000` Calories, a total of `11000` Calories.
>     
> *   The fourth Elf is carrying food with `7000`, `8000`, and `9000` Calories, a total of `24000` Calories.
>     
> *   The fifth Elf is carrying one food item with `10000` Calories.
>     
> 
> In case the Elves get hungry and need extra snacks, they need to know which Elf to ask: they'd like to know how many Calories are being carried by the Elf carrying the **most** Calories. In the example above, this is `24000` (carried by the fourth Elf).
> 
> Find the Elf carrying the most Calories. **How many total Calories is that Elf carrying?**

#### Solution

We will start by implementing the function that takes a `List<String>` (our input file) and aggregate it into a `List<Int>` that represents the Calories each Elf is carrying.

The solution will be as follow:

*   use the `fold` function over the list, and start with a list with `0` Calories.
    
*   For each step, if the current line isn't empty we will update the Calories stored in the latest entry in the list.
    
*   If the is empty, we will create a new entry in the list, and set it to `0` - this will represent the next elf in the list.
    
*   Find the maximum value in the list of Calories and return it.
    

```kotlin
private fun caloriesCount(input: List<String>) =
    input.fold(mutableListOf(0)) { elves, snack ->
        if (snack.isEmpty()) elves.add(0)
        else elves[elves.lastIndex] = elves.last() + snack.toInt()
        elves
    }
```

Now let's find the maximum calories in the list:

```kotlin
// readPuzzleInput(1) is a method reading the day1 input file
fun caloriesCountTopElf(input: List<String> = readPuzzleInput(1)) =
    caloriesCount(input).maxOf { it }
```

Now we can run the code and test it:

```kotlin
fun main() {
    println("The 🧝 carrying the most calories have: ${caloriesCountTopElf()}")
}
```

The result of this execution would be:

```plaintext
The 🧝 carrying the most calories have: 69177
```

## Part 2

#### Task

> By the time you calculate the answer to the Elves' question, they've already realized that the Elf carrying the most Calories of food might eventually **run out of snacks**.
> 
> To avoid this unacceptable situation, the Elves would instead like to know the total Calories carried by the **top three** Elves carrying the most Calories. That way, even if one of those Elves runs out of snacks, they still have two backups.
> 
> In the example above, the top three Elves are the fourth Elf (with `24000` Calories), then the third Elf (with `11000` Calories), then the fifth Elf (with `10000` Calories). The sum of the Calories carried by these three elves is `45000`.
> 
> Find the top three Elves carrying the most Calories. **How many Calories are those Elves carrying in total?**

#### Solution

*   We will re-use the `caloriesCount()` function from the previous step
    
*   Sort the results
    
*   Take the top 3 calories in the list and sum them together
    

```kotlin
fun caloriesCountTopThreeElves(input: List<String> = readPuzzleInput(1)) =
    caloriesCount(input)
        .sorted()
        .takeLast(3)
        .sum()
```

We can now combine both solutions as follow:

```kotlin
fun main() {
    println("The 🧝 carrying the most calories have: ${caloriesCountTopElf()}")
    println("The 3 top 🧝 carrying the most calories have: ${caloriesCountTopThreeElves()}")
}
```

And our output would be:

```plaintext
The 🧝 carrying the most calories have: 69177
The 3 top 🧝 carrying the most calories have: 207456
```

See you on the next challenge!