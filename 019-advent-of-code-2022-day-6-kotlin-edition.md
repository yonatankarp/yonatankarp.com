---
title: "Advent of Code 2022 (Day 6) - Kotlin Edition"
subtitle: ""
slug: advent-of-code-2022-day-6-kotlin-edition
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

> The preparations are finally complete; you and the Elves leave camp on foot and begin to make your way toward the *star* fruit grove.
> 
> As you move through the dense undergrowth, one of the Elves gives you a handheld *device*. He says that it has many fancy features, but the most important one to set up right now is the *communication system*.
> 
> However, because he's heard you have [significant](https://adventofcode.com/2016/day/6) [experience](https://adventofcode.com/2016/day/25) [dealing](https://adventofcode.com/2019/day/7) [with](https://adventofcode.com/2019/day/9) [signal-based](https://adventofcode.com/2019/day/16) [systems](https://adventofcode.com/2021/day/25), he convinced the other Elves that it would be okay to give you their one malfunctioning device - surely you'll have no problem fixing it.
> 
> As if inspired by comedic timing, the device emits a few colorful sparks.
> 
> To be able to communicate with the Elves, the device needs to *lock on to their signal*. The signal is a series of seemingly-random characters that the device receives one at a time.
> 
> To fix the communication system, you need to add a subroutine to the device that detects a *start-of-packet marker* in the datastream. In the protocol being used by the Elves, the start of a packet is indicated by a sequence of *four characters that are all different*.
> 
> The device will send your subroutine a datastream buffer (your puzzle input); your subroutine needs to identify the first position where the four most recently received characters were all different. Specifically, it needs to report the number of characters from the beginning of the buffer to the end of the first such four-character marker.
> 
> For example, suppose you receive the following datastream buffer:
> 
> ```kotlin
> mjqjpqmgbljsphdztnvjfqwrcgsmlb
> ```
> 
> After the first three characters (`mjq`) have been received, there haven't been enough characters received yet to find the marker. The first time a marker could occur is after the fourth character is received, making the most recent four characters `mjqj`. Because `j` is repeated, this isn't a marker.
> 
> The first time a marker appears is after the *seventh* character arrives. Once it does, the last four characters received are `jpqm`, which are all different. In this case, your subroutine should report the value `7`, because the first start-of-packet marker is complete after 7 characters have been processed.
> 
> Here are a few more examples:
> 
> *   `bvwbjplbgvbhsrlpgdmjqwftvncz`: first marker after character `5`
>     
> *   `nppdvjthqldpwncqszvftbrmjlhg`: first marker after character `6`
>     
> *   `nznrnfrfntjfmvfwmzdfjlvtqnbhcprsg`: first marker after character `10`
>     
> *   `zcfzfwzzqfrljwzlrfnpqdbhtmscgvjw`: first marker after character `11`
>     
> 
> *How many characters need to be processed before the first start-of-packet marker is detected?*

### Solution

*   Implement a method that checks whether or not a given string has duplicated characters in it.
    
*   Break the string into a rolling window of 4 characters including an index.
    
*   Find the 1st window that does not have duplicates and return its index
    

Let's start by implementing the `hasDuplicates()` functionality:

```kotlin
fun String.hasDuplicates(ignoreCases: Boolean = true): Boolean {
    val string = if (ignoreCases) this.lowercase() else this
    string.asSequence()
        .groupingBy { it }
        .aggregate { _, _: Int?, _, first ->
            if (first) 1
            else return true
        }
        .let { return false }
}
```

With that, we can now implement our main task:

```kotlin
 fun solvePart1(): Int
     input
        .windowed(size = 4, step = 1, partialWindows = false)
        .withIndex()
        .first { !it.value.hasDuplicates() }
        .index + 4 // adding 4 because we want the last chart index
```

That's it. let's look at our test case. Today is a spacial day as we received multiple input examples. Therefore, I decided to use the `jUnit` feature of `@ParamtereizedTest` that receives the test case as the input:

```kotlin
internal class Part1ExampleInputArgumentsProvider : ArgumentsProvider {
    override fun provideArguments(context: ExtensionContext): Stream<out Arguments> =
        Stream.of(
            Arguments.of(TestCase(input = "mjqjpqmgbljsphdztnvjfqwrcgsmlb", output = 7)),
            Arguments.of(TestCase(input = "bvwbjplbgvbhsrlpgdmjqwftvncz", output = 5)),
            Arguments.of(TestCase(input = "nppdvjthqldpwncqszvftbrmjlhg", output = 6)),
            Arguments.of(TestCase(input = "nznrnfrfntjfmvfwmzdfjlvtqnbhcprsg", output = 10)),
            Arguments.of(TestCase(input = "zcfzfwzzqfrljwzlrfnpqdbhtmscgvjw", output = 11)),
        )
}

@ParameterizedTest
@ArgumentsSource(Part1ExampleInputArgumentsProvider::class)
fun `Part 1 - Example`(testCase: TestCase) {
    val day06 = Day06(testCase.input)
    assertEquals(testCase.output, day06.solvePart1())
}

@Test
fun `Part 1 - Real Input`() {
    val day06 = Day06(resourceAsString("2022/day06.txt"))
    assertEquals(1794, day06.solvePart1())
}
```

## Part 2

### Task

> Your device's communication system is correctly detecting packets, but still isn't working. It looks like it also needs to look for *messages*.
> 
> A *start-of-message marker* is just like a start-of-packet marker, except it consists of *14 distinct characters* rather than 4.
> 
> Here are the first positions of start-of-message markers for all of the above examples:
> 
> *   `mjqjpqmgbljsphdztnvjfqwrcgsmlb`: first marker after character `19`
>     
> *   `bvwbjplbgvbhsrlpgdmjqwftvncz`: first marker after character `23`
>     
> *   `nppdvjthqldpwncqszvftbrmjlhg`: first marker after character `23`
>     
> *   `nznrnfrfntjfmvfwmzdfjlvtqnbhcprsg`: first marker after character `29`
>     
> *   `zcfzfwzzqfrljwzlrfnpqdbhtmscgvjw`: first marker after character `26`
>     
> 
> *How many characters need to be processed before the first start-of-message marker is detected?*

### Solution

This solution here is fairly simple, we can change the window size from `4` to `14`. Let's refactor our previous solution to support that:

```kotlin
private fun solve(windowSize: Int) =
    input
        .windowed(size = windowSize, step = 1, partialWindows = false)
        .withIndex()
        .first { !it.value.hasDuplicates() }
        .index + windowSize

fun solvePart1(): Int = solve(PACKAGE_MARKER_WINDOW_SIZE)

fun solvePart2(): Int = solve(MESSAGE_MARKER_WINDOW_SIZE)

companion object {
    private const val PACKAGE_MARKER_WINDOW_SIZE = 4
    private const val MESSAGE_MARKER_WINDOW_SIZE = 14
}
```

And we're done, let's look at the test cases:

```kotlin
internal class Part2ExampleInputArgumentsProvider : ArgumentsProvider {
    override fun provideArguments(context: ExtensionContext): Stream<out Arguments> =
        Stream.of(
            Arguments.of(TestCase(input = "mjqjpqmgbljsphdztnvjfqwrcgsmlb", output = 19)),
            Arguments.of(TestCase(input = "bvwbjplbgvbhsrlpgdmjqwftvncz", output = 23)),
            Arguments.of(TestCase(input = "nppdvjthqldpwncqszvftbrmjlhg", output = 23)),
            Arguments.of(TestCase(input = "nznrnfrfntjfmvfwmzdfjlvtqnbhcprsg", output = 29)),
            Arguments.of(TestCase(input = "zcfzfwzzqfrljwzlrfnpqdbhtmscgvjw", output = 26)),
        )
}

ParameterizedTest
@ArgumentsSource(Part2ExampleInputArgumentsProvider::class)
fun `Part 2 - Example`(testCase: TestCase) {
    val day06 = Day06(testCase.input)
    assertEquals(testCase.output, day06.solvePart2())
}

@Test
fun `Part 2 - Real Input`() {
    val day06 = Day06(resourceAsString("2022/day06.txt"))
    assertEquals(2851, day06.solvePart2())
}
```

All of the code that was presented in the article, along with the utility classes that are used are available in [**my GitHub account.**](https://github.com/yonatankarp/advent-of-code)

See you on the next challenge!
