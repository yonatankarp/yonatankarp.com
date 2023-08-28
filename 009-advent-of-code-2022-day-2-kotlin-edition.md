---
title: "Advent of Code 2022 (Day 2) - Kotlin Edition"
subtitle: ""
slug: advent-of-code-2022-day-2-kotlin-edition
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
> ```kotlin
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

### Solution

*   Read lines from input, and parse them into game choices
    
*   Compare the 2 results and calculate points accordingly
    

To handle our game choices we will use an enum, where each option also holds its score in the game:

```kotlin
enum class GameChoice(val score: Int) {
    ROCK(1),
    PAPER(2),
    SCISSORS(3)
}
```

Next, we will parse our input file, and build a list of choices of the players:

```kotlin
typealias RoundStrategy = Pair<GameChoice, GameChoice>

class StrategyGuideParser {
    fun loadStrategy(fileName: String): List<RoundStrategy> =
         FileLoader.loadInput(fileName)
            .filter { it.isNotBlank() }
            .map { it.split(" ") }
            .map {
                val (opponent, mine) = it
                RoundStrategy(
                    opponentChoose = toGameChoice(opponent),
                    roundEndStrategy = toGameChoice(mine)
                )
            }
            .toList()

    private fun toGameChoice(choice: String) =
        when (choice) {
            "A", "X" -> ROCK
            "B", "Y" -> PAPER
            "C", "Z" -> SCISSORS
            else -> throw RuntimeException("Unknown type $encryptChoice")
        }
}
```

Next, lets build the business logic, where we decide whether or not we won the game, and how many points are we going to get for it. The code is long and confusing, but don't worry, we will refactor this in part 2!

```kotlin
class GameResultCalculator {
    fun calculate(game: List<RoundStrategy>) =
        game.sumOf { roundScore(it) }

    private fun roundScore(round: RoundStrategy) =
        when {
            // Win
            round.first == ROCK && round.second == PAPER -> win(PAPER)
            round.first == PAPER && round.second == SCISSORS -> win(SCISSORS)
            round.first == SCISSORS && round.second == ROCK -> win(ROCK)
            // Tie
            round.first == ROCK && round.second == ROCK -> tie(ROCK)
            round.first == PAPER && round.second == PAPER -> tie(PAPER)
            round.first == SCISSORS && round.second == SCISSORS -> tie(SCISSORS)
            // Lose
            round.first == PAPER && round.second == ROCK -> lose(ROCK)
            round.first == ROCK && round.second == SCISSORS -> lose(SCISSORS)
            round.first == SCISSORS && round.second == PAPER -> lose(PAPER)

            else -> throw RuntimeException("Combo unknown: $round")
        }

    private fun win(choice: GameChoice) = GAME_WIN_SCORE + choice.score
    private fun tie(choice: GameChoice) = GAME_TIE_SCORE + choice.score
    private fun lose(choice: GameChoice) = GAME_LOSE_SCORE + choice.score

    companion object {
        private const val GAME_WIN_SCORE = 6
        private const val GAME_TIE_SCORE = 3
        private const val GAME_LOSE_SCORE = 0
    }
}
```

Finally, let's bring it all together:

```kotlin
fun main() {
    val strategy = StrategyGuideParser().loadStrategy("/day2/input.txt")
    val result = GameResultCalculator().calculate(strategy)
    println(result)
}
```

# Part 2

### Task

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

### Solution

*   Introduce a new enum that represents the end-of-game strategy (win/lose/draw).
    
*   Create a model for a run strategy that will hold the opponent's choice and the end of a round strategy
    
*   Implement a different strategy per game choice
    

Let's add our new enum:

```kotlin
enum class RoundEndStrategy {
    WIN,
    LOSE,
    DRAW
}
```

Our new round strategy model will look as follows:

```kotlin
data class RoundStrategy(
    val opponentChoose: GameChoice,
    val roundEndStrategy: RoundEndStrategy
)
```

We will have to amend our parsing logic as well to support the new type we introduced:

```kotlin
class StrategyGuideParser {
    fun loadStrategy(fileName: String): List<RoundStrategy> =
         FileLoader.loadInput(fileName)
            .filter { it.isNotBlank() }
            .map { it.split(" ") }
            .map {
                val (opponent, mine) = it
                RoundStrategy(
                    opponentChoose = toGameChoice(opponent),
                    roundEndStrategy = toRoundEndStrategy(mine)
                )
            }
            .toList()

    private fun toGameChoice(opponentChoose: String) =
        when (opponentChoose) {
            "A" -> ROCK
            "B" -> PAPER
            "C" -> SCISSORS
            else -> throw RuntimeException("Unknown type $opponentChoose")
        }

    private fun toRoundEndStrategy(strategy: String) =
        when (strategy) {
            "X" -> LOSE
            "Y" -> DRAW
            "Z" -> WIN
            else -> throw RuntimeException("Unknown type $strategy")
        }
}
```

But most importantly, we need to change our main business logic, as now we have to decide which choice to select based on the round strategy:

```kotlin
abstract class GameStrategy {
    abstract fun roundResult(roundEndStrategy: RoundEndStrategy): Int

    protected fun win(choice: GameChoice) = GAME_WIN_SCORE + choice.score
    protected fun draw(choice: GameChoice) = GAME_TIE_SCORE + choice.score
    protected fun lose(choice: GameChoice) = GAME_LOSE_SCORE + choice.score

    companion object {
        private const val GAME_WIN_SCORE = 6
        private const val GAME_TIE_SCORE = 3
        private const val GAME_LOSE_SCORE = 0
    }
}

class RockStrategy : GameStrategy() {
    override fun roundResult(roundEndStrategy: RoundEndStrategy) =
        when (roundEndStrategy) {
            WIN -> win(PAPER)
            LOSE -> lose(SCISSORS)
            DRAW -> draw(ROCK)
        }
}

class PaperStrategy : GameStrategy() {
    override fun roundResult(roundEndStrategy: RoundEndStrategy) =
        when (roundEndStrategy) {
            WIN -> win(SCISSORS)
            LOSE -> lose(ROCK)
            DRAW -> draw(PAPER)
        }
}

class ScissorsStrategy : GameStrategy() {
    override fun roundResult(roundEndStrategy: RoundEndStrategy) =
        when (roundEndStrategy) {
            WIN -> win(ROCK)
            LOSE -> lose(PAPER)
            DRAW -> draw(SCISSORS)
        }
}
```

Let's apply our new strategies:

```kotlin
class GameResultCalculator {
    private val rockStrategy = RockStrategy()
    private val paperStrategy = PaperStrategy()
    private val scissorsStrategy = ScissorsStrategy()

    fun calculate(game: List<RoundStrategy>) = game.sumOf { roundScore(it) }

    private fun roundScore(round: RoundStrategy) =
        when (round.opponentChoose) {
            ROCK -> rockStrategy.roundResult(round.roundEndStrategy)
            PAPER -> paperStrategy.roundResult(round.roundEndStrategy)
            SCISSORS -> scissorsStrategy.roundResult(round.roundEndStrategy)
        }
}
```

That's it, we're done! I hope that you enjoyed it as much as I did!

%%[contact-me]
