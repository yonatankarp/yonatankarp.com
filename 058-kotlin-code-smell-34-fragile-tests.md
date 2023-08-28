---
title: "Kotlin Code Smell 34 - Fragile Tests"
subtitle: "Tests are our safety net. When their integrity is in doubt, we're at risk"
slug: kotlin-code-smell-34-fragile-tests
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/5X5-iyx1SP0/upload/72295aca8e728b619e89241949ff9e19.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Testing Guide: Eliminating Fragile Tests for Better Development"
seoDescription: "Improve your Kotlin testing practices by identifying and resolving fragile tests, ensuring reliable and consistent results for better software development."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Steer clear of non-deterministic tests..

## Problem

* Lack of determinism

* Eroding confidence

* Time squandered


## Solution

1. Ensure that the test is fully deterministic. Eradicate any potential for unpredictable behavior.

2. Eliminate test coupling.


## Examples

The terms "Fragile," "Intermittent," "Sporadic," and "Erratic" are often used interchangeably when discussing problematic tests in many development environments.

However, such tests erode the trust developers place in their test suites.

Such tests are best avoided.

## Sample Code

### Wrong

```kotlin
import org.junit.jupiter.api.Assertions.assertEquals
import org.junit.jupiter.api.Test

abstract class SetTest {

    protected abstract fun constructor(): MutableSet<String>

    @Test
    fun `test add empty`() {
        val set = constructor()
        set.add("green")
        set.add("blue")
        assertEquals("[green, blue]", set.toString())
        // This is fragile since the outcome hinges on the set's
        // ordering, which isn't predefined and can vary based on
        // the set's implementation.
    }
}
```

### Right

```kotlin
import org.junit.jupiter.api.Test
import kotlin.test.assertEquals
import kotlin.test.assertTrue

abstract class SetTest {

    protected abstract fun constructor(): MutableSet<String>

    @Test
    fun `test add empty`() {
        val set = constructor()
        set.add("green")
        assertEquals("[green]", set.toString())
    }

    @Test
    fun `test entry at single entry`() {
        val set = createFromArgs("red")
        val result = set.contains("red")
        assertTrue(result)
    }
}
```

%%[contact-me]

## Conclusion

Fragile tests often indicate system coupling and manifest as non-deterministic or unpredictable behaviors.

Addressing and resolving these erratic tests can drain considerable developer time and energy.

## Credits

* [Code Smell 51 - Fragile Tests](https://maximilianocontieri.com/code-smell-52-fragile-tests) by @[Maximiliano Contieri](@mcsee)
