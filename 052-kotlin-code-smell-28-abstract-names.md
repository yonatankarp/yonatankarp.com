---
title: "Kotlin Code Smell 28 - Abstract Names"
subtitle: "Unleashing the Magic of Meaningful Naming"
slug: kotlin-code-smell-28-abstract-names
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/wQLAGv4_OYs/upload/0da07320cfbf4e807972d868892c17a3.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Mastering Kotlin Naming Conventions: Overcoming Abstract Names in Your"
seoDescription: "Optimize your Kotlin code by avoiding abstract names and using meaningful naming conventions, improving code quality, readability, and maintainability."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

## Problem

* Implemental Naming
* Meaningless names
* Broken [**MAPPER**](https://maximilianocontieri.com/what-is-wrong-with-software) and [**Bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) to real-world entities.

## Solution

1. Choose meaningful names.
2. Find metaphors.
3. Avoid words like *abstract*, *base*, *generic*, *helper*, *data*, *info*, etc.
4. Use [**rules**](https://maximilianocontieri.com/what-exactly-is-a-name-part-ii-rehab) for naming.

## Sample Code

### Wrong

```kotlin
class Repository {
    // ...
}

class MeetingCollection {
    // ...
}

class AccountsComposite {
    // ...
}

class NoteArray {
    // ...
}

class LogCollector {
    // ...
}

abstract class SearcherBase {
    // ...
}

abstract class AbstractTransportation {
    // ...
}
```

### Right

```kotlin
class Schedule {
    // ...
}

class Portfolio {
    // ...
}

class NoteBook {
    // ...
}

class Journal {
    // ...
}

class Vehicle {
    // ...
}
```

%%[contact-me]

## Conclusion

Finding names is the last thing we should do in our designs. Unless we have a clear business understanding, good names emerge at the end after defining behavior and protocol boundaries.


## Credits

* [Code Smell 38 - Abstract Names](https://maximilianocontieri.com/code-smell-38-abstract-names) by @[Maximiliano Contieri](@mcsee)
