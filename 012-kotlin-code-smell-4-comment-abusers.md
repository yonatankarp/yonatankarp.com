---
title: "Kotlin Code Smell 4 - Comment Abusers"
subtitle: "Comments are coupled with implementation and hardly maintained."
slug: kotlin-code-smell-4-comment-abusers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/zoCDWPuiRuA/upload/v1669836538952/BPgqADuAfO.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid Comment Overuse in Code: Code Smell 4"
seoDescription: "Learn why excessive code comments can harm code quality and discover solutions to improve code maintainability and readability. Find examples and tips."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> ***TL;DR:*** *Leave comments just for important design decisions. Don't explain the obvious!*

# Problems

* Maintainability
    
* Obsolete Documentation
    
* Readability
    
* Code and comments duplication.
    

# Solutions

* Refactor methods.
    
* Rename methods to more declarative ones.
    
* Break methods into smaller and easier for understanding methods.
    
* If a comment describes what a method does, [**name the method with this description**](https://maximilianocontieri.com/refactoring-005-replace-comment-with-function-name).
    
* comment on important design decisions only!
    

# Examples

* Libraries
    
* Class Comments
    
* Method Comments
    

# Sample Code

### Wrong

```kotlin
/**
 * ChatBotConnectionHelper is used to create connection strings to
 * Bot Platform Use this class with getString() function to get 
 * connection string to the platform.
 */
class ChatBotConnectionHelper(
    var id: String
) {
    // Get Connection String from Chatbot
    fun getString(): String = TODO()
}
```

### Right

```kotlin
class ChatBotConnectionSequenceGenerator(
    private val name: String
) {
    fun connectionSequence(): Unit = TODO()
}
```

%%[contact-me]

# Conclusion

Leave comments just for important design decisions. Don't comment on a method with a bad name, rename it. Code changes over time, while documentation rarely does, which causes your code to end up with outdated documentation at best, or wrong documentation at worst.

# More info

* [Refactoring Guru](https://refactoring.guru/es/smells/comments)
    
* [What is in a name](https://maximilianocontieri.com/what-exactly-is-a-name-part-i-the-quest)
    
* [Comments as a bad sign](https://dev.to/alexbunardzic/code-comments-are-a-sign-that-something-s-off-19e1)
    
* [How to comment your code](https://arter.dev/how-to-comment-your-code-like-a-boss)
    

# Credits

* [Code Smell 05 - Comment Abusers](https://maximilianocontieri.com/code-smell-05-comment-abusers) by @[Maximiliano Contieri](@mcsee)
