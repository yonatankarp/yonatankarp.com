# ChatGPT will not replace engineers any time soon...

> **TL;DR:** ChatGPT generates terrible buggy code faster than the average software developer.

For those of you who lives under a rock in the last couple of weeks, here's Wikipedia's definition of ChatGPT:

> **ChatGPT**, which stands for **Chat Generative Pre-trained Transformer**, is a [chatbot](https://en.wikipedia.org/wiki/Chatbot) developed by [OpenAI](https://en.wikipedia.org/wiki/OpenAI). ChatGPT is built on top of OpenAI's [GPT-3.5](https://en.wikipedia.org/wiki/GPT-3) family of [large language models](https://en.wikipedia.org/wiki/Language_model), and is [fine-tuned](https://en.wikipedia.org/wiki/Transfer_learning) with both [supervised](https://en.wikipedia.org/wiki/Supervised_learning) and [reinforcement learning](https://en.wikipedia.org/wiki/Reinforcement_learning) techniques.
> 
> ChatGPT was launched as a prototype in November 2022, and quickly garnered attention for its detailed responses and articulate answers across many domains of knowledge. Its uneven factual accuracy was identified as a significant drawback.

After reading about it online none-stop, I was very curious to test this new amazing AI. It took only a short period of time before I was extremely impressed with the results of it. Yet, that made me think... Is this the end of engineering? Can ChatGPT take requirements and implement them into code?

To test this, I decided to ask ChatGPT to solve the [Advent of Code 2022 - Day 10](https://adventofcode.com/2022/day/10) puzzle. Since I'm usually writing my solutions in functional programming, I decided to add this to the requirements as well.

## Iteration 1

> write me a code in kotlin using functional programming that solves this puzzle:
> 
> &lt;puzzle description...&gt;

After a few seconds I received the following code:

```kotlin
fun solvePuzzle(program: List<Instruction>): List<Int> {
    // Initialize X register with value 1
    var x = 1

    // Initialize a list to store signal strengths
    val signalStrengths = mutableListOf<Int>()

    // Loop through the program, executing each instruction
    program.forEachIndexed { index, instruction ->
        when (instruction) {
            is Noop -> {
                // Do nothing
            }
            is AddX -> {
                x += instruction.v
            }
        }

        // Check if the current cycle is the 20th, 60th, 100th, etc.
        if (index % 40 == 19) {
            // Calculate the signal strength for this cycle and add
            // it to the list
            signalStrengths.add(index * x)
        }
    }

    return signalStrengths
}
```

## Iteration 2

While the code is really nice, I've noticed that it does not match the input of the puzzle, so I've asked ChatGPT to accept the input as `List<String>`.

> Please replace the input of the function with a List of strings where each string represents a single operation in the input file

The response was very quick:

```kotlin
fun solvePuzzle(program: List<String>): List<Int> {
    // Initialize X register with value 1
    var x = 1

    // Initialize a list to store signal strengths
    val signalStrengths = mutableListOf<Int>()

    // Loop through the program, executing each instruction
    program.forEachIndexed { index, instruction ->
        when (instruction) {
            "noop" -> {
                // Do nothing
            }
            else -> {
                // Split the instruction string into the operation
                // and value
                val parts = instruction.split(" ")
                val operation = parts[0]
                val value = parts[1].toInt()

                // Increment the X register by the value
                x += value
            }
        }

        // Check if the current cycle is the 20th, 60th, 100th, etc.
        if (index % 40 == 19) {
            // Calculate the signal strength for this cycle and add it
            // to the list
            signalStrengths.add(index * x)
        }
    }

    return signalStrengths
}
```

## Iteration 3

Next, I noticed that the output is wrong. We want to receive the sum of all signals and not a list of them.

> There's a bug in the code, the solution should return the sum of all signals and not a list of integers

This time, ChatGPT didn't even apologize for the mistake (am I a Karan for it?), and just updated the solution:

```kotlin
fun solvePuzzle(program: List<String>): Int {
    // Initialize X register with value 1
    var x = 1

    // Initialize a variable to store the sum of the signal strengths
    var signalSum = 0

    // Loop through the program, executing each instruction
    program.forEachIndexed { index, instruction ->
        when (instruction) {
            "noop" -> {
                // Do nothing
            }
            else -> {
                // Split the instruction string into the operation
                // and value
                val parts = instruction.split(" ")
                val operation = parts[0]
                val value = parts[1].toInt()

                // Increment the X register by the value
                x += value
            }
        }

        // Check if the current cycle is the 20th, 60th, 100th, etc.
        if (index % 40 == 19) {
            // Calculate the signal strength for this cycle and add
            // it to the sum
            signalSum += index * x
        }
    }

    return signalSum
}
```

## Iteration 4

Note that this version already includes some garbage code in it. `val operation = parts[0]` is not used anywhere in this function. this could be easily solved by using an anonymous variable. for example:

```kotlin
// Split the instruction string into the operation and value
val (_, value) = instruction.split(" ")
```

Moreover, I immediately noticed another bug in the code. `addx` operations should take 2 cycles, while `noop` takes only 1. No panic, let's ask ChatGPT to fix the issue!

> there is a bug in your solution, addx operation should take 2 cycles while noop takes only 1

The solution quickly arrived:

```kotlin
fun solvePuzzle(program: List<String>): Int {
    // Initialize X register with value 1
    var x = 1

    // Initialize a variable to store the sum of the signal strengths
    var signalSum = 0

    // Initialize a variable to keep track of the current cycle
    var currentCycle = 0

    // Loop through the program, executing each instruction
    program.forEach { instruction ->
        when (instruction) {
            "noop" -> {
                // Increment the current cycle by 1
                currentCycle++
            }
            else -> {
                // Split the instruction string into the operation
                // and value
                val parts = instruction.split(" ")
                val operation = parts[0]
                val value = parts[1].toInt()

                // Increment the current cycle by 2
                currentCycle += 2

                // Increment the X register by the value
                x += value
            }
        }

        // Check if the current cycle is the 20th, 60th, 100th, etc.
        if (currentCycle % 40 == 19) {
            // Calculate the signal strength for this cycle and add
            // it to the sum
            signalSum += currentCycle * x
        }
    }

    return signalSum
}
```

## Iteration 5

After that, I noticed yet another bug in the code. since `addx` takes 2 cycles, we need to check if the signal sum should be `currencyCycle * x` or `currentCycle * (x + value)` depending on the schedule of the cycle. I asked ChatGPT to fix the issue:

> there is another bug in the code. since addx takes 2 cycles, you should either include or exclude the new value when calculating the signal sum depending on the schedule of it.

Quickly I received an updated version of the code:

```kotlin
fun solvePuzzle(program: List<String>): Int {
    // Initialize X register with value 1
    var x = 1

    // Initialize a variable to store the sum of the signal strengths
    var signalSum = 0

    // Initialize a variable to keep track of the current cycle
    var currentCycle = 0

    // Loop through the program, executing each instruction
    program.forEach { instruction ->
        when (instruction) {
            "noop" -> {
                // Increment the current cycle by 1
                currentCycle++

                // Check if the current cycle is the 20th,
                // 60th, 100th, etc.
                if (currentCycle % 40 == 19) {
                    // Calculate the signal strength for this cycle
                    // and add it to the sum
                    signalSum += currentCycle * x
                }
            }
            else -> {
                // Split the instruction string into the operation
                // and value
                val parts = instruction.split(" ")
                val operation = parts[0]
                val value = parts[1].toInt()

                // Increment the current cycle by 2
                currentCycle += 2

                // Check if the current cycle is the 20th, 60th,
                // 100th, etc.
                if (currentCycle % 40 == 19) {
                    // Calculate the signal strength for this cycle
                    // and add it to the sum
                    signalSum += currentCycle * x
                }

                // Increment the X register by the value
                x += value
            }
        }
    }

    return signalSum
}
```

## Iteration 6

Let's avoid the obvious code duplication we have here. The solution is still wrong. `addx` does not take into account whether it should include the value in the calculation.

```kotlin
fun solvePuzzle(program: List<String>): Int {
    // Initialize X register with value 1
    var x = 1

    // Initialize a variable to store the sum of the signal strengths
    var signalSum = 0

    // Initialize a variable to keep track of the current cycle
    var currentCycle = 0

    // Loop through the program, executing each instruction
    program.forEach { instruction ->
        when (instruction) {
            "noop" -> {
                // Increment the current cycle by 1
                currentCycle++

                // Check if the current cycle is the 20th, 60th,
                // 100th, etc.
                if (currentCycle % 40 == 19) {
                    // Calculate the signal strength for this cycle 
                    // and add it to the sum
                    signalSum += currentCycle * x
                }
            }
            else -> {
                // Split the instruction string into the operation
                // and value
                val parts = instruction.split(" ")
                val operation = parts[0]
                val value = parts[1].toInt()

                // Increment the current cycle by 2
                currentCycle += 2

                // Check if the current cycle is the 19th or the
                // 20th, 59th or 60th, 99th or 100th, etc.
                if (currentCycle % 40 == 19 || currentCycle % 40 == 0) {
                    // Calculate the signal strength for this cycle
                    // and add it to the sum
                    signalSum += currentCycle * x
                }

                // Increment the X register by the value
                x += value
            }
        }
    }

    return signalSum
}
```

While the code finally checks which iteration we are in `addx`. It still doesn't take the value evaluation into account. At this point, I was already exhausted, and (not so) slowly but surely we started to have a spaghetti code.

Moreover, ChatGPT just lost the connection to my browser, and I didn't want to start all over again

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671605807209/vGFKZF5Je.png align="center")

For comparison, this is the code I wrote to solve the task. While it's far from perfect, it's simpler, easier to reason about, and also working!

```kotlin
fun solvePuzzle(program: List<String>): Int {
        var register = 1
        var cycle = 1
        return program
            .filter { it.isNotBlank() }
            .map { instruction ->
                cycle++
                var signal = cycle.getCycleSignal(cycle, register)
                if (instruction.startsWith("addx")) {
                    // addx operation takes 2 cycles
                    cycle++
                    val (_, value) = instruction.split(" ")
                    register += value.toInt()
                    signal += cycle.getCycleSignal(cycle, register)
                }

                signal
            }
            .sumOf { it }
    }

private fun Int.getCycleSignal(cycle: Int, register: Int) =
    if (cycle % 40 == 20) cycle * register
    else 0
```

## Conclusion

While ChatGPT seems to work nicely for human interactions, it works very purely when writing code from requirements. It can be used as inspiration for your code, but as you have seen, at some point I just started to debug someone else's code, and the code was very messy. That's why I don't believe that ChatGPT will replace us any time soon. We're safe. For now.

## More Information:

*   [ChatGPT](https://chat.openai.com/chat)
    
*   [ChatGPT - Wikipedia](https://en.wikipedia.org/wiki/ChatGPT)