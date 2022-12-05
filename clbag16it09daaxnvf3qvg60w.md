# Advent of Code 2022 - Day 2 - Kotlin Edition

## Day 2 - Rock Paper Scissors

### Part 1

#### Task

> The Elves begin to set up camp on the beach. To decide whose tent gets to be closest to the snack storage, a giant [Rock Paper Scissors](https://en.wikipedia.org/wiki/Rock_paper_scissors) tournament is already in progress.
> 
> Rock Paper Scissors is a game between two players. Each game contains many rounds; in each round, the players each simultaneously choose one of Rock, Paper, or Scissors using a hand shape. Then, a winner for that round is selected: Rock defeats Scissors, Scissors defeats Paper, and Paper defeats Rock. If both players choose the same shape, the round instead ends in a draw.
> 
> Appreciative of your help yesterday, one Elf gives you an *encrypted strategy guide* (your puzzle input) that they say will be sure to help you win. "The first column is what your opponent is going to play: `A` for Rock, `B` for Paper, and `C` for Scissors. The second column--" Suddenly, the Elf is called away to help with someone's tent.
> 
> The second column, you reason, must be what you should play in response: `X` for Rock, `Y` for Paper, and `Z` for Scissors. Winning every time would be suspicious, so the responses must have been carefully chosen.
> 
> The winner of the whole tournament is the player with the highest score. Your *total score* is the sum of your scores for each round. The score for a single round is the score for the *shape you selected* (1 for Rock, 2 for Paper, and 3 for Scissors) plus the score for the *outcome of the round* (0 if you lost, 3 if the round was a draw, and 6 if you won).
> 
> Since you can't be sure if the Elf is trying to help you or trick you, you should calculate the score you would get if you were to follow the strategy guide.
> 
> For example, suppose you were given the following strategy guide:
> 
> ```plaintext
> A Y
> B X
> C Z
> ```
> 
> This strategy guide predicts and recommends the following:
> 
> *   In the first round, your opponent will choose Rock (`A`), and you should choose Paper (`Y`). This ends in a win for you with a score of *8* (2 because you chose Paper + 6 because you won).
>     
> *   In the second round, your opponent will choose Paper (`B`), and you should choose Rock (`X`). This ends in a loss for you with a score of *1* (1 + 0).
>     
> *   The third round is a draw with both players choosing Scissors, giving you a score of 3 + 3 = *6*.
>     
> 
> In this example, if you were to follow the strategy guide, you would get a total score of `15` (8 + 1 + 6).
> 
> *What would your total score be if everything goes exactly according to your strategy guide?*

#### Solution

*   For each row in the file, we will create a pair of game choices (rock, paper, or scissors).
    
*   We will calculate whether we won this round, and add the points of our choice.
    
*   We will sum all points together as the final score of the game.
    

Let's start by defining our mapping. We will define a new Enum. it would include the 3 different options available in the game. We will also create a mapper from each of the letters to the correct game choice. Last but not least, we will take the 2 characters from the line. create a pair of `GameChoice` from it (e.g. `A Z` would map to `ROCK SCISSORS`)

```kotlin
enum class GameChoice(val value: Int) {
    ROCK(1),
    PAPER(2),
    SCISSORS(3);
}

fun Char.toGameChoice(): GameChoice =
    when (this) {
        'A', 'X' -> ROCK
        'B', 'Y' -> PAPER
        'C', 'Z' -> SCISSORS
        else -> throw IllegalArgumentException("Game choice cannot be $this")
    }

fun toGameChoice(line: String): Pair<GameChoice, GameChoice> {
    require(line.length == 3) { "Line input should include exactly 3 characters!" }
    return line[0].toGameChoice() to line[2].toGameChoice()
}
```

Now, we will define the score per round based on our choice and our opponent's choice:

```kotlin
private object Scores {
    const val WIN = 6
    const val DRAW = 3
    const val LOOSE = 0
}

private fun GameChoice.scoreAgainst(opponent: GameChoice) =
    when {
        this.win(opponent) -> Scores.WIN
        this == opponent -> Scores.DRAW
        else -> Scores.LOOSE
    }    

fun GameChoice.win(opponent: GameChoice): Boolean =
    when {
        this == ROCK && opponent == SCISSORS ||
                this == PAPER && opponent == ROCK ||
                this == SCISSORS && opponent == PAPER -> true
        else -> false
    }
```

Now let's take everything and combine it into a single function that will calculate the score of the game. it would iterate over the lines in the file, and for each line map it into game choices. Then, it will use the choices to calculate the score of the round. Finally, it would sum all the scores into the game's total score.

```kotlin
fun gameScore(input: List<String> = readPuzzleInput(2)) =
    input.sumOf {
        if (it.isBlank()) 0
        else {
            val (opponent, mine) = toGameChoice(it)
            mine.scoreAgainst(opponent) + mine.value
        }
    }
```

That's it, we're done!

### Part 2

#### Task

> The Elf finishes helping with the tent and sneaks back over to you. "Anyway, the second column says how the round needs to end: `X` means you need to lose, `Y` means you need to end the round in a draw, and `Z` means you need to win. Good luck!"
> 
> The total score is still calculated in the same way, but now you need to figure out what shape to choose so the round ends as indicated. The example above now goes like this:
> 
> *   In the first round, your opponent will choose Rock (`A`), and you need the round to end in a draw (`Y`), so you also choose Rock. This gives you a score of 1 + 3 = *4*.
>     
> *   In the second round, your opponent will choose Paper (`B`), and you choose Rock so you lose (`X`) with a score of 1 + 0 = *1*.
>     
> *   In the third round, you will defeat your opponent's Scissors with Rock for a score of 1 + 6 = *7*.
>     
> 
> Now that you're correctly decrypting the ultra top secret strategy guide, you would get a total score of `12`.
> 
> Following the Elf's instructions for the second column, *what would your total score be if everything goes exactly according to your strategy guide?*

#### Solution

We will have to make a few small amendments to our previous solution:

*   Introduce a representation for the round strategy we want to take (Win/Draw/Loose)
    
*   Update the parsing function. it would parse each line into a pair of GameChoice and `RoundStrategy`.
    
*   Create a new parser for `RoundStrategy`.
    
*   Introduce a function that decides which choice to select to win or lose based on the `GameChoice`. In case of a draw, we will return the same choice as the opponent.
    

Let's start with our new Enum and a mapper for it:

```kotlin
enum class RoundStrategy {
    WIN,
    DRAW,
    LOOSE
}

fun Char.toRoundStrategy(): RoundStrategy =
    when (this) {
        'X' -> LOOSE
        'Y' -> DRAW
        'Z' -> WIN
        else -> throw IllegalArgumentException("Game choice cannot be $this")
    }
```

Now, let's implement the decision of which `GameChoice`. Our decision would be based on our strategy and the opponent selection:

```kotlin
fun GameChoice.wins() =
    when(this) {
        ROCK -> SCISSORS
        PAPER -> ROCK
        SCISSORS -> PAPER
    }

fun GameChoice.looseTo() =
    when(this) {
        ROCK -> PAPER
        PAPER -> SCISSORS
        SCISSORS -> ROCK
    }

fun RoundStrategy.toGameChoice(opponent: GameChoice): GameChoice =
    when (this) {
        WIN -> opponent.looseTo()
        DRAW -> opponent
        LOOSE -> opponent.wins()
    }
```

Now, all we need is to wrap everything up together. Our method would parse each line in the file. But this time, it would take the opponent's choice and our strategy. It will then return a pair of `GameChoice`'s that will give us the strategy we wanted (win, draw or lose).

```kotlin
fun toGameChoice(line: String): Pair<GameChoice, GameChoice> {
    require(line.length == 3) { "Line input should include exactly 3 characters!" }
    val opponent = line[0].toGameChoice()
    val roundStrategy = line[2].toRoundStrategy()
    val mine = roundStrategy.toGameChoice(opponent)
    return opponent to mine
}
```

That's it, we're done and we can calculate our score in the game using