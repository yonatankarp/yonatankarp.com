---
title: "Advent of Code 2022 (Day 5) - Kotlin Edition"
subtitle: ""
slug: advent-of-code-2022-day-5-kotlin-edition
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

> The expedition can depart as soon as the final supplies have been unloaded from the ships. Supplies are stored in stacks of marked *crates*, but because the needed supplies are buried under many other crates, the crates need to be rearranged.
> 
> The ship has a *giant cargo crane* capable of moving crates between stacks. To ensure none of the crates get crushed or fall over, the crane operator will rearrange them in a series of carefully-planned steps. After the crates are rearranged, the desired crates will be at the top of each stack.
> 
> The Elves don't want to interrupt the crane operator during this delicate procedure, but they forgot to ask her *which* crate will end up where, and they want to be ready to unload them as soon as possible so they can embark.
> 
> They do, however, have a drawing of the starting stacks of crates *and* the rearrangement procedure (your puzzle input). For example:
> 
> ```kotlin
>     [D]    
> [N] [C]    
> [Z] [M] [P]
>  1   2   3 
> 
> move 1 from 2 to 1
> move 3 from 1 to 3
> move 2 from 2 to 1
> move 1 from 1 to 2
> ```
> 
> In this example, there are three stacks of crates. Stack 1 contains two crates: crate `Z` is on the bottom, and crate `N` is on top. Stack 2 contains three crates; from bottom to top, they are crates `M`, `C`, and `D`. Finally, stack 3 contains a single crate, `P`.
> 
> Then, the rearrangement procedure is given. In each step of the procedure, a quantity of crates is moved from one stack to a different stack. In the first step of the above rearrangement procedure, one crate is moved from stack 2 to stack 1, resulting in this configuration:
> 
> ```kotlin
> [D]        
> [N] [C]    
> [Z] [M] [P]
>  1   2   3
> ```
> 
> In the second step, three crates are moved from stack 1 to stack 3. Crates are moved *one at a time*, so the first crate to be moved (`D`) ends up below the second and third crates:
> 
> ```kotlin
>         [Z]
>         [N]
>     [C] [D]
>     [M] [P]
>  1   2   3
> ```
> 
> Then, both crates are moved from stack 2 to stack 1. Again, because crates are moved *one at a time*, crate `C` ends up below crate `M`:
> 
> ```kotlin
>         [Z]
>         [N]
> [M]     [D]
> [C]     [P]
>  1   2   3
> ```
> 
> Finally, one crate is moved from stack 1 to stack 2:
> 
> ```kotlin
>         [Z]
>         [N]
>         [D]
> [C] [M] [P]
>  1   2   3
> ```
> 
> The Elves just need to know *which crate will end up on top of each stack*; in this example, the top crates are `C` in stack 1, `M` in stack 2, and `Z` in stack 3, so you should combine these together and give the Elves the message `CMZ`.
> 
> *After the rearrangement procedure completes, what crate ends up on top of each stack?*

### Solution

*   The input file will be read as 1 string and would be split into 2 blocks based on the empty line.
    
*   The 1st block would be parsed to build the stacks' initial state, and the 2nd to build the instructions.
    
*   We will represent the stacks as a list of lists. The 1st index in the list is the top container in the stack.
    
*   We will parse the identifier of the stacks by chucks of 4, as each one would be at most 4 characters (e.g. \[A\] ).
    
*   We will use regex to parse our instructions. They would be stored in a data class, and they will contain the following fields: `amount`, `from stack`, and `to stack`.
    
*   Each amount that we pull from one stack needs to be reversed as we're inserting them on the new stack in reversed oreder.
    

Let's start to code!

First, we will parse our stacks and instructions:

```kotlin
private val stacks =  
    parseStacks(input.first().split(System.lineSeparator()))
private val instructions =
    parseInstructions(input.last().split(System.lineSeparator()))

private fun parseStacks(input: List<String>): List<MutableList<Char>> {
    val numberOfStacks = 
        input
            .last() // The lines contain stack numbers
            .trim() // Remove extra spaces at the beginning/end
            .substringAfterLast(" ")
            .toInt()
    val stacks = List(numberOfStacks) { mutableListOf<Char>() }
    input.dropLast(1) // Remove the stacks numbers
        .map { row ->
            row.chunked(4) // Each stack name is 4 chars (E.g. "[A] ")
                .forEachIndexed { index, containerName ->
                    if (containerName.isNotBlank()) 
                        stacks[index].add(containerName[1])
                }
        }

    return stacks
}

private fun parseInstructions(input: List<String>): List<Instruction> =
    input
        .map {
            val (_, amount, from, to) = """move (\d+) from (\d+) to (\d+)""".toRegex().find(it)!!.groupValues
            Instruction(amount.toInt(), from.toInt() - 1, to.toInt() - 1)
        }
```

Notes:

*   During the puzzle work, I make a mistake in the regex and wrote `(\d)+` instead of `(\d+)`. This cause the regex to catch only lines that have either 1 digit or the same digit multiple times. I spend a couple of hours solving this bug. the moral of the story is, whenever using Regex test them at [https://regex101.com](https://regex101.com/)!
    
*   We're using a new Kotlin feature that [deconstructs a list](https://kotlinlang.org/docs/destructuring-declarations.html) into its elements, and also the `_` to describe an anonymous value that we are not going to use.
    

Now let's wrap everything into the solution of part 1:

*   We will use the `List` interface method `removeFirst()` that removes and returns the 1st element from the list
    
*   We will repeat the process `amount` times, and revert the results.
    

```kotlin
fun solvePart1(): String =
    instructions
        .forEach { instruction ->
            val toMove = MutableList(instruction.amount) { stacks[instruction.from].removeFirst() }.reverse()
            stacks[instruction.to].addAll(0, toMove)
        }
        .let { stacks.joinToString("") { it.first().toString() } }
```

That is all. Time to run our test cases:

```kotlin
@Test
fun `Part 1 - Example`() {
    val day05 = Day05(exampleInput)
    assertEquals("CMZ", day05.solvePart1())
}

@Test
fun `Part 1 - Real Input`() {
    val day05 = Day05(resourceSplitOnBlankLines("2022/day05.txt"))
    assertEquals("JCMHLVGMG", day05.solvePart1())
}
```

## Part 2

### Task

> As you watch the crane operator expertly rearrange the crates, you notice the process isn't following your prediction.
> 
> Some mud was covering the writing on the side of the crane, and you quickly wipe it away. The crane isn't a CrateMover 9000 - it's a *CrateMover 9001*.
> 
> The CrateMover 9001 is notable for many new and exciting features: air conditioning, leather seats, an extra cup holder, and *the ability to pick up and move multiple crates at once*.
> 
> Again considering the example above, the crates begin in the same configuration:
> 
> ```kotlin
>     [D]    
> [N] [C]    
> [Z] [M] [P]
>  1   2   3
> ```
> 
> Moving a single crate from stack 2 to stack 1 behaves the same as before:
> 
> ```kotlin
> [D]        
> [N] [C]    
> [Z] [M] [P]
>  1   2   3
> ```
> 
> However, the action of moving three crates from stack 1 to stack 3 means that those three moved crates *stay in the same order*, resulting in this new configuration:
> 
> ```kotlin
>         [D]
>         [N]
>     [C] [Z]
>     [M] [P]
>  1   2   3
> ```
> 
> Next, as both crates are moved from stack 2 to stack 1, they *retain their order* as well:
> 
> ```kotlin
>         [D]
>         [N]
> [C]     [Z]
> [M]     [P]
>  1   2   3
> ```
> 
> Finally, a single crate is still moved from stack 1 to stack 2, but now it's crate `C` that gets moved:
> 
> ```kotlin
>         [D]
>         [N]
>         [Z]
> [M] [C] [P]
>  1   2   3
> ```
> 
> In this example, the CrateMover 9001 has put the crates in a totally different order: `MCD`.
> 
> Before the rearrangement process finishes, update your simulation so that the Elves know where they should stand to be ready to unload the final supplies. *After the rearrangement procedure completes, what crate ends up on top of each stack?*

### Solution

This part would just be a small refactoring for our previous step. We can extract the functionality had had into a shared function that would either revert the inserted containers or not based on a flag.

```kotlin
private fun solve(isOneContainerOnly: Boolean = true) = 
    instructions
        .forEach { instruction ->
            val toMove = MutableList(instruction.amount) { stacks[instruction.from].removeFirst() }
                .apply { if (isOneContainerOnly) this.reverse() }
            stacks[instruction.to].addAll(0, toMove)
        }
        .let { stacks.joinToString("") { it.first().toString() } }

fun solvePart1(): String = solve(true)

fun solvePart2(): String = solve(false)
```

And we're done. Let's run our tests!

```kotlin
@Test
fun `Part 2 - Example`() {
    val day05 = Day05(exampleInput)
    assertEquals("MCD", day05.solvePart2())
}

@Test
fun `Part 2 - Real Input`() {
    val day05 = Day05(resourceSplitOnBlankLines("2022/day05.txt"))
    assertEquals("LVMRWSSPZ", day05.solvePart2())
}
```

All of the code that was presented in the article, along with the utility classes that are used are available in [**my GitHub account.**](https://github.com/yonatankarp/advent-of-code)

See you on the next challenge!
