---
title: "Advent of Code 2022 (Day 7) - Kotlin Edition"
subtitle: ""
slug: advent-of-code-2022-day-7-kotlin-edition
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

> You can hear birds chirping and raindrops hitting leaves as the expedition proceeds. Occasionally, you can even hear much louder sounds in the distance; how big do the animals get out here, anyway?
> 
> The device the Elves gave you has problems with more than just its communication system. You try to run a system update:
> 
> ```kotlin
> $ system-update --please --pretty-please-with-sugar-on-top
> Error: No space left on device
> ```
> 
> Perhaps you can delete some files to make space for the update?
> 
> You browse around the filesystem to assess the situation and save the resulting terminal output (your puzzle input). For example:
> 
> ```kotlin
> $ cd /
> $ ls
> dir a
> 14848514 b.txt
> 8504156 c.dat
> dir d
> $ cd a
> $ ls
> dir e
> 29116 f
> 2557 g
> 62596 h.lst
> $ cd e
> $ ls
> 584 i
> $ cd ..
> $ cd ..
> $ cd d
> $ ls
> 4060174 j
> 8033020 d.log
> 5626152 d.ext
> 7214296 k
> ```
> 
> The filesystem consists of a tree of files (plain data) and directories (which can contain other directories or files). The outermost directory is called `/`. You can navigate around the filesystem, moving into or out of directories and listing the contents of the directory you're currently in.
> 
> Within the terminal output, lines that begin with `$` are *commands you executed*, very much like some modern computers:
> 
> *   `cd` means *change directory*. This changes which directory is the current directory, but the specific result depends on the argument:
>     
>     *   `cd x` moves *in* one level: it looks in the current directory for the directory named `x` and makes it the current directory.
>         
>     *   `cd ..` moves *out* one level: it finds the directory that contains the current directory, then makes that directory the current directory.
>         
>     *   `cd /` switches the current directory to the outermost directory, `/`.
>         
> *   `ls` means *list*. It prints out all of the files and directories immediately contained by the current directory:
>     
>     *   `123 abc` means that the current directory contains a file named `abc` with size `123`.
>         
>     *   `dir xyz` means that the current directory contains a directory named `xyz`.
>         
> 
> Given the commands and output in the example above, you can determine that the filesystem looks visually like this:
> 
> ```kotlin
> - / (dir)
>   - a (dir)
>     - e (dir)
>       - i (file, size=584)
>     - f (file, size=29116)
>     - g (file, size=2557)
>     - h.lst (file, size=62596)
>   - b.txt (file, size=14848514)
>   - c.dat (file, size=8504156)
>   - d (dir)
>     - j (file, size=4060174)
>     - d.log (file, size=8033020)
>     - d.ext (file, size=5626152)
>     - k (file, size=7214296)
> ```
> 
> Here, there are four directories: `/` (the outermost directory), `a` and `d` (which are in `/`), and `e` (which is in `a`). These directories also contain files of various sizes.
> 
> Since the disk is full, your first step should probably be to find directories that are good candidates for deletion. To do this, you need to determine the *total size* of each directory. The total size of a directory is the sum of the sizes of the files it contains, directly or indirectly. (Directories themselves do not count as having any intrinsic size.)
> 
> The total sizes of the directories above can be found as follows:
> 
> *   The total size of directory `e` is *584* because it contains a single file `i` of size 584 and no other directories.
>     
> *   The directory `a` has total size *94853* because it contains files `f` (size 29116), `g` (size 2557), and `h.lst` (size 62596), plus file `i` indirectly (`a` contains `e` which contains `i`).
>     
> *   Directory `d` has total size *24933642*.
>     
> *   As the outermost directory, `/` contains every file. Its total size is *48381165*, the sum of the size of every file.
>     
> 
> To begin, find all of the directories with a total size of *at most 100000*, then calculate the sum of their total sizes. In the example above, these directories are `a` and `e`; the sum of their total sizes is `95437` (94853 + 584). (As in this example, this process can count files more than once!)
> 
> Find all of the directories with a total size of at most 100000. *What is the sum of the total sizes of those directories?*

### Solution

For this day's solution, I decided to go a bit over the edge. The solution is based on the [Composite design pattern](https://refactoring.guru/design-patterns/composite). So, let's describe it:

*   Create an abstract class `FileSystem`. It will represent a general resource on our file system.
    
*   Create a `File` class that inherits from `FileSystem`
    
*   Create a `Directory` class that inherits from `FileSystem`. It will hold a map of resources (file or directory) by their names.
    
    *   The `Directory` class will have the following operations:
        
        *   `addFile()` - add a new file to our resources map
            
        *   `getOrCreateDirectory()` - return a Directory object by its name or create a new one if none exists.
            
        *   `findDirectoriesBy()` - returning all directories that match a given predicate.
            

Parsing our input is also a bit of a messy mission. To solve the parsing we will use a queue that will help us to understand where we are in the directory tree. We will do so by adding a new `Directory` every time we use the `$ cd` command, and removing it from when we use either the `cd ..` or `cd /` commands. the `dir` command will create a new empty `Directory` in our current context

Let's dive into the implementation!

We will start by defining our resource classes:

```kotlin
abstract class FileSystem(val name: String) {
    abstract val size: Int
}

class File(name: String, override val size: Int) : FileSystem(name)

class Directory(
    name: String,
    private val content: MutableMap<String, FileSystem> = mutableMapOf()
) : FileSystem(name) {
    override val size: Int
        get() = content.values.sumOf { it.size }

    fun addFile(file: File) {
        content[file.name] = file
    }

    fun getDirectory(directoryName: String) : Directory =
        content[directoryName] as Directory

    fun createDirectory(directoryName: String) {
        content[directoryName] = Directory(directoryName)
    }

    fun findDirectoriesBy(predicate: (Directory) -> Boolean): List<FileSystem> {
        val directories = content
            .values
            .toList()
            .filterIsInstance<Directory>()
        return directories.filter { predicate(it) } +
                directories.flatMap { it.findDirectoriesBy(predicate) }
    }
}
```

For our parsing, we are going to follow this structure:

*   `$ ls` command would be ignored, as it's not a `FileSystem` resource
    
*   `$ cd`
    
    *   if the command changes context to the root directory we will remove all objects from the queue that are not the root
        
    *   if the command change context to the parent we will simply remove the 1st element in the queue.
        
    *   otherwise, we will change the context into the given directory and add it on the top of the queue.
        
*   `dir` will create a new empty `Directory` object in the context of the current resource in the queue.
    

At the end of the parsing, we will return the last element in the queue as it will hold the `root` directory.

```kotlin
private fun parseInput(input: List<String>): Directory {
    val callStack = ArrayDeque<Directory>().apply { add(Directory("/")) }
    input.forEach { item ->
        when {
            item == "$ ls" -> {}
            item == "$ cd /" -> callStack.removeIf { it.name != "/" }
            item == "$ cd .." -> callStack.removeFirst()
            item.startsWith("dir") -> {
                val (_, name) = item.split(" ")
                callStack.first().createDirectory(name)
            }
            item.startsWith("$ cd") -> {
                val (_, _, name) = item.split(" ")
                callStack.addFirst(callStack.first().getDirectory(name))
            }
            else -> {
                val (size, fileName) = item.split(" ")
                callStack.first().addFile(File(fileName, size.toInt()))
            }
        }
    }

    return callStack.last()
}
```

Now let's connect everything together. We will take the directory tree, filter by directories with sizes smaller than `100,000` and sum the results.

```kotlin
private val directoryTree = parseInput(input)

fun solvePart1(): Int =
    directoryTree
        .findDirectoriesBy { it.size <= DIRECTORIES_LESS_THEN }
        .sumOf { it.size }

companion object {
    private const val DIRECTORIES_LESS_THEN = 100_000
}
```

Finally, we can run our tests and validate that everything is working.

```kotlin
@Test
fun `Part 1 - Example`() {
    val day07 = Day07(exampleInput)
    assertEquals(95437, day07.solvePart1())
}

@Test
fun `Part 1 - Real Input`() {
    val day07 = Day07(resourceAsList("2022/day07.txt"))
    assertEquals(1350966, day07.solvePart1())
}
```

## Part 2

### Task

> Now, you're ready to choose a directory to delete.
> 
> The total disk space available to the filesystem is `70000000`. To run the update, you need unused space of at least `30000000`. You need to find a directory you can delete that will *free up enough space* to run the update.
> 
> In the example above, the total size of the outermost directory (and thus the total amount of used space) is `48381165`; this means that the size of the *unused* space must currently be `21618835`, which isn't quite the `30000000` required by the update. Therefore, the update still requires a directory with total size of at least `8381165` to be deleted before it can run.
> 
> To achieve this, you have the following options:
> 
> *   Delete directory `e`, which would increase unused space by `584`.
>     
> *   Delete directory `a`, which would increase unused space by `94853`.
>     
> *   Delete directory `d`, which would increase unused space by `24933642`.
>     
> *   Delete directory `/`, which would increase unused space by `48381165`.
>     
> 
> Directories `e` and `a` are both too small; deleting them would not free up enough space. However, directories `d` and `/` are both big enough! Between these, choose the *smallest*: `d`, increasing unused space by `24933642`.
> 
> Find the smallest directory that, if deleted, would free up enough space on the filesystem to run the update. *What is the total size of that directory?*

### Solution

To solve this part we need to:

*   Calculate the available memory of the device
    
*   Calculate the missing memory
    
*   filter all directories that have a size equal to or greater than the missing memory and pick the minimal size.
    

```kotlin
fun solvePart2(): Int {
    val unusedMemory = TOTAL_FILESYSTEM_SPACE - directoryTree.size
    val missingMemory = UPDATE_REQUIRED_SPACE - unusedMemory
    return directoryTree
        .findDirectoriesBy { it.size > missingMemory }
        .minBy { it.size }.size
}

companion object {
    private const val DIRECTORIES_LESS_THEN = 100_000
    private const val TOTAL_FILESYSTEM_SPACE = 70_000_000
    private const val UPDATE_REQUIRED_SPACE = 30_000_000
}
```

That's it. Let's run our tests:

```kotlin
@Test
fun `Part 2 - Example`() {
    val day07 = Day07(exampleInput)
    assertEquals(24933642, day07.solvePart2())
}

@Test
fun `Part 2 - Real Input`() {
    val day07 = Day07(resourceAsList("2022/day07.txt"))
    assertEquals(6296435, day07.solvePart2())
}
```

All of the code that was presented in the article, along with the utility classes that are used are available in [**my GitHub account.**](https://github.com/yonatankarp/advent-of-code)

See you on the next challenge!

%%[contact-me]
