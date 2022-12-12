# Advent of Code 2022 - Day 8 - Kotlin Edition

## Part 1

### Task

> The expedition comes across a peculiar patch of tall trees all planted carefully in a grid. The Elves explain that a previous expedition planted these trees as a reforestation effort. Now, they're curious if this would be a good location for a [tree house](https://en.wikipedia.org/wiki/Tree_house).
> 
> First, determine whether there is enough tree cover here to keep a tree house *hidden*. To do this, you need to count the number of trees that are *visible from outside the grid* when looking directly along a row or column.
> 
> The Elves have already launched a [quadcopter](https://en.wikipedia.org/wiki/Quadcopter) to generate a map with the height of each tree (your puzzle input). For example:
> 
> ```kotlin
> 30373
> 25512
> 65332
> 33549
> 35390
> ```
> 
> Each tree is represented as a single digit whose value is its height, where `0` is the shortest and `9` is the tallest.
> 
> A tree is *visible* if all of the other trees between it and an edge of the grid are *shorter* than it. Only consider trees in the same row or column; that is, only look up, down, left, or right from any given tree.
> 
> All of the trees around the edge of the grid are *visible* - since they are already on the edge, there are no trees to block the view. In this example, that only leaves the *interior nine trees* to consider:
> 
> *   The top-left `5` is *visible* from the left and top. (It isn't visible from the right or bottom since other trees of height `5` are in the way.)
>     
> *   The top-middle `5` is *visible* from the top and right.
>     
> *   The top-right `1` is not visible from any direction; for it to be visible, there would need to only be trees of height *0* between it and an edge.
>     
> *   The left-middle `5` is *visible*, but only from the right.
>     
> *   The center `3` is not visible from any direction; for it to be visible, there would need to be only trees of at most height `2` between it and an edge.
>     
> *   The right-middle `3` is *visible* from the right.
>     
> *   In the bottom row, the middle `5` is *visible*, but the `3` and `4` are not.
>     
> 
> With 16 trees visible on the edge and another 5 visible in the interior, a total of `21` trees are visible in this arrangement.
> 
> Consider your map; *how many trees are visible from outside the grid?*

### Solution

For this challenge, I decided to use some utility classes I've found online. You can find the links below.

We will start by defining those utility classes.

*   `Position` - a simple position class contains the position coordinates (x and y).
    
*   `Direction` - an enum class of the direction we want to take (up, down, left, and right), and allowing some operations around it.
    
*   `Grid` - an `N x N` matrix that will hold our input. Moreover, it will include functionality that is available on matrics.
    

**Position**

```kotlin
data class Position(val x: Int, val y: Int) {

    companion object {
        fun allDeltas(
            includeDiagonals: Boolean = false
        ): List<Position> {
            val results = mutableListOf<Position>()
            for (dy in -1..1) {
                for (dx in -1..1) {
                    if (dy == 0 && dx == 0) continue
                    if (includeDiagonals || dy == 0 || dx == 0) {
                        results.add(Position(dx, dy))
                    }
                }
            }
            return results
        }
    }
}
```

**Direction**

```kotlin
enum class Direction(val dx: Int, val dy: Int) {
    Up(dx = 0, dy = -1),
    Right(dx = 1, dy = 0),
    Down(dx = 0, dy = 1),
    Left(dx = -1, dy = 0);
}
```

**Grid**

```kotlin
class Grid(
    val size: Int,
    initializer: () -> Char = { '\u0000' }
) {
    private val data: Array<CharArray> =
        Array(size) { CharArray(size) { initializer() } }

    /**
     * Get the value at the given position, using a Pos for
     * addressing.
     */
    operator fun get(position: Position): Char =
        data[position.y][position.x]

    /**
     * A list of all the positions in this grid
     */
    val keys: List<Position> by lazy {
        (0 until size)
            .flatMap { y -> (0 until size)
                .map { x -> Position(x, y) } }
    }

    companion object {
        /**
         * Generates a new Grid based on the given input. If the input
         * is not square the parts not covered by the input will be
         * left un-initialized (null characters)
         */
        fun parse(input: List<String>): Grid {
            val grid = Grid(max(input.size, input[0].length))
            for (y in input.indices) {
                for (x in input[0].indices) {
                    grid.data[y][x] = input[y][x]
                }
            }
            return grid
        }
    }
}
```

Now that we have all our util classes, we can jump to the solution itself.

The solution would be as follow:

*   For each tree, create a list of all available positions in the 4 allowed directions.
    
*   For any of the directions, check all trees are lower than our current tree.
    
*   Count the results.
    

```kotlin
private val grid = Grid.parse(input)

private fun allPositionsFromATree(tree: Position): List<List<Position>> =
    listOf(
        // down
        (tree.y + 1 until grid.size).map { Position(tree.x, it) }, 
        // up
        (tree.y - 1 downTo 0).map { Position(tree.x, it) },
        // right
        (tree.x + 1 until grid.size).map { Position(it, tree.y) }, 
        // left
        (tree.x - 1 downTo 0).map { Position(it, tree.y) } 
    )

fun solvePart1(): Int =
    grid
        .keys
        .map { it to allPositionsFromATree(it) }
        .count { treeToDirections ->
            treeToDirections.second.any { direction ->
                // all on empty list returns true, thus tree on the 
                // edges counted as visible
                direction.all { position ->
                    grid[treeToDirections.first] > grid[position]
                }
            }
        }
```

And we're done. We can now run our test cases:

```kotlin
@Test
fun `Part 1 - Example`() {
    val day08 = Day08(exampleInput)
    assertEquals(21, day08.solvePart1())
}

@Test
fun `Part 1 - Real Input`() {
    val day08 = Day08(resourceAsList("2022/day08.txt"))
    assertEquals(1698, day08.solvePart1())
}
```

## Part 2

### Task

> Content with the amount of tree cover available, the Elves just need to know the best spot to build their tree house: they would like to be able to see a lot of *trees*.
> 
> To measure the viewing distance from a given tree, look up, down, left, and right from that tree; stop if you reach an edge or at the first tree that is the same height or taller than the tree under consideration. (If a tree is right on the edge, at least one of its viewing distances will be zero.)
> 
> The Elves don't care about distant trees taller than those found by the rules above; the proposed tree house has large [eaves](https://en.wikipedia.org/wiki/Eaves) to keep it dry, so they wouldn't be able to see higher than the tree house anyway.
> 
> In the example above, consider the middle `5` in the second row:
> 
> ```kotlin
> 30373
> 25512
> 65332
> 33549
> 35390
> ```
> 
> *   Looking up, its view is not blocked; it can see `1` tree (of height `3`).
>     
> *   Looking left, its view is blocked immediately; it can see only `1` tree (of height `5`, right next to it).
>     
> *   Looking right, its view is not blocked; it can see `2` trees.
>     
> *   Looking down, its view is blocked eventually; it can see `2` trees (one of height `3`, then the tree of height `5` that blocks its view).
>     
> 
> A tree's *scenic score* is found by *multiplying together* its viewing distance in each of the four directions. For this tree, this is `4` (found by multiplying `1 * 1 * 2 * 2`).
> 
> However, you can do even better: consider the tree of height `5` in the middle of the fourth row:
> 
> ```kotlin
> 30373
> 25512
> 65332
> 33549
> 35390
> ```
> 
> *   Looking up, its view is blocked at `2` trees (by another tree with a height of `5`).
>     
> *   Looking left, its view is not blocked; it can see `2` trees.
>     
> *   Looking down, its view is also not blocked; it can see `1` tree.
>     
> *   Looking right, its view is blocked at `2` trees (by a massive tree of height `9`).
>     
> 
> This tree's scenic score is `8` (`2 * 2 * 1 * 2`); this is the ideal spot for the tree house.
> 
> Consider each tree on your map. *What is the highest scenic score possible for any tree?*

### Solution

We will reuse a small part of the previous question code for this part.

*   For each tree, create a list of all available positions in the 4 allowed directions
    
*   For each direction, calculate the score of the tree. The score is based on the number of visible trees in each direction.
    
*   Take maximal value.
    

We will start by introducing a function that will calculate the number of visible trees:

```kotlin
private fun List<Position>.numberOfVisible(ownHeight: Char): Int {
    var visible = 0
    for (tree in this) {
        val otherHeight = grid[tree]
        if (otherHeight < ownHeight) visible++
        else return visible + 1
    }
    return visible
}
```

We will now implement the solution. We will calculate the score for each tree based on the total number of visible trees in the direction. We will take the maximal, and if there is none, we will return the value of `0`.

```kotlin
fun solvePart2(): Int =
    grid
        .keys
        .maxOfOrNull { tree ->
            allPositionsFromATree(tree).fold(1) { acc, positions ->
                acc * positions.numberOfVisible(grid[tree])
            }
        } ?: 0
```

We're done, let's run the test cases!

```kotlin
@Test
fun `Part 2 - Example`() {
    val day08 = Day08(exampleInput)
    assertEquals(8, day08.solvePart2())
}

@Test
fun `Part 2 - Real Input`() {
    val day08 = Day08(resourceAsList("2022/day08.txt"))
    assertEquals(672280, day08.solvePart2())
}
```

All of the code that was presented in the article, along with the utility classes that are used are available in [**my GitHub account.**](https://github.com/yonatankarp/advent-of-code)

See you on the next challenge!

## Links

*   The code of this post is available [here](https://github.com/yonatankarp/advent-of-code/commit/5e24d301f1b7a1c312b92a083371b550c76398f5).
    
*   [AoC](https://github.com/nibarius/aoc) by Niklas Barsk