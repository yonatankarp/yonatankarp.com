---
title: "Advent of Code 2022 (Day 3) - Kotlin Edition"
subtitle: ""
slug: advent-of-code-2022-day-3-kotlin-edition
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

> One Elf has the important job of loading all of the [rucksacks](https://en.wikipedia.org/wiki/Rucksack) with supplies for the jungle journey. Unfortunately, that Elf didn't quite follow the packing instructions, and so a few items now need to be rearranged.
> 
> Each rucksack has two large *compartments*. All items of a given type are meant to go into exactly one of the two compartments. The Elf that did the packing failed to follow this rule for exactly one item type per rucksack.
> 
> The Elves have made a list of all of the items currently in each rucksack (your puzzle input), but they need your help finding the errors. Every item type is identified by a single lowercase or uppercase letter (that is, `a` and `A` refer to different types of items).
> 
> The list of items for each rucksack is given as characters all on a single line. A given rucksack always has the same number of items in each of its two compartments, so the first half of the characters represent items in the first compartment, while the second half of the characters represent items in the second compartment.
> 
> For example, suppose you have the following list of contents from six rucksacks:
> 
> ```plaintext
> vJrwpWtwJgWrhcsFMMfFFhFp
> jqHRNqRjqzjGDLGLrsFMfFZSrLrFZsSL
> PmmdzqPrVvPwwTWBwg
> wMqvLMZHhHMvwLHjbvcjnnSBnvTQFn
> ttgJtRGJQctTZtZT
> CrZsJsPPZsGzwwsLwLmpwMDw
> ```
> 
> *   The first rucksack contains the items `vJrwpWtwJgWrhcsFMMfFFhFp`, which means its first compartment contains the items `vJrwpWtwJgWr`, while the second compartment contains the items `hcsFMMfFFhFp`. The only item type that appears in both compartments is lowercase `p`.
>     
> *   The second rucksack's compartments contain `jqHRNqRjqzjGDLGL` and `rsFMfFZSrLrFZsSL`. The only item type that appears in both compartments is uppercase `L`.
>     
> *   The third rucksack's compartments contain `PmmdzqPrV` and `vPwwTWBwg`; the only common item type is uppercase `P`.
>     
> *   The fourth rucksack's compartments only share item type `v`.
>     
> *   The fifth rucksack's compartments only share item type `t`.
>     
> *   The sixth rucksack's compartments only share item type `s`.
>     
> 
> To help prioritize item rearrangement, every item type can be converted to a *priority*:
> 
> *   Lowercase item types `a` through `z` have priorities 1 through 26.
>     
> *   Uppercase item types `A` through `Z` have priorities 27 through 52.
>     
> 
> In the above example, the priority of the item type that appears in both compartments of each rucksack is 16 (`p`), 38 (`L`), 42 (`P`), 22 (`v`), 20 (`t`), and 19 (`s`); the sum of these is `157`.
> 
> Find the item type that appears in both compartments of each rucksack. *What is the sum of the priorities of those item types?*

### Solution

*   Create a map of letters to their priorities.
    
*   For each line, split it into 2 equal halves.
    
*   Filter only letters in the 1st half that are also included in the 2nd half.
    
*   Sum their priorities
    

```kotlin
private val priorities = (('a'..'z').zip(1..26) +   
    ('A'..'Z').zip(27..52)).toMap()

fun solvePart1(): Int =
    input
        .map { it.substring(0, it.length / 2) to it.substring(it.length / 2) }
        .flatMap { it.first.filter { c -> it.second.contains(c) }.toSet() }
        .sumOf { priorities[it]!! }
```

Time to run the test cases:

```kotlin
@Test
fun `Part 1 - Example`() {
    val day03 = Day03(exampleInput)
    assertEquals(157, day03.solvePart1())
}

@Test
fun `Part 1 - Real Input`() {
    val day03 = Day03(resourceAsList("2022/day03.txt"))
    assertEquals(8153, day03.solvePart1())
}
```

## Part 2

### Task

> As you finish identifying the misplaced items, the Elves come to you with another issue.
> 
> For safety, the Elves are divided into groups of three. Every Elf carries a badge that identifies their group. For efficiency, within each group of three Elves, the badge is the *only item type carried by all three Elves*. That is, if a group's badge is item type `B`, then all three Elves will have item type `B` somewhere in their rucksack, and at most two of the Elves will be carrying any other item type.
> 
> The problem is that someone forgot to put this year's updated authenticity sticker on the badges. All of the badges need to be pulled out of the rucksacks so the new authenticity stickers can be attached.
> 
> Additionally, nobody wrote down which item type corresponds to each group's badges. The only way to tell which item type is the right one is by finding the one item type that is *common between all three Elves* in each group.
> 
> Every set of three lines in your list corresponds to a single group, but each group can have a different badge item type. So, in the above example, the first group's rucksacks are the first three lines:
> 
> ```kotlin
> vJrwpWtwJgWrhcsFMMfFFhFp
> jqHRNqRjqzjGDLGLrsFMfFZSrLrFZsSL
> PmmdzqPrVvPwwTWBwg
> ```
> 
> And the second group's rucksacks are the next three lines:
> 
> ```kotlin
> wMqvLMZHhHMvwLHjbvcjnnSBnvTQFn
> ttgJtRGJQctTZtZT
> CrZsJsPPZsGzwwsLwLmpwMDw
> ```
> 
> In the first group, the only item type that appears in all three rucksacks is lowercase `r`; this must be their badges. In the second group, their badge item type must be `Z`.
> 
> Priorities for these items must still be found to organize the sticker attachment efforts: here, they are 18 (`r`) for the first group and 52 (`Z`) for the second group. The sum of these is `70`.
> 
> Find the item type that corresponds to the badges of each three-Elf group. *What is the sum of the priorities of those item types?*

### Solution

This solution will be almost the same as the one above with a few minor changes:

*   Each iteration will take 3 rows into account instead of 1.
    
*   For each chunk, filter letters in the 1st row that are also included in the 2nd and 3rd rows.
    
*   Again, sum up all the prioritizes
    

```kotlin
fun solvePart2(): Int =
    input
        .chunked(3)
        .flatMap { it.first().filter { c -> c in it[1] && c in it[2] }.toSet() }
        .sumOf { priorities[it]!! }
```

And we're done, let's run the test cases of part 2:

```kotlin
Test
fun `Part 2 - Example`() {
    val day03 = Day03(exampleInput)
    assertEquals(70, day03.solvePart2())
}

@Test
fun `Part 2 - Real Input`() {
    val day03 = Day03(resourceAsList("2022/day03.txt"))
    assertEquals(2342, day03.solvePart2())
}
```

All of the code that was presented in the article, along with the utility classes that are used are available in [**my GitHub account.**](https://github.com/yonatankarp/advent-of-code)

See you on the next challenge!

%%[contact-me]
