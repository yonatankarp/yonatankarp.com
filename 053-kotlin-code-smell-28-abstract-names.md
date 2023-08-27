---
title: "Kotlin Code Smell 29 - Regular Expression Abusers"
subtitle: "From Spaghetti Code to Readable Magic"
slug: kotlin-code-smell-29-regular-expression-abusers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Kl4LNdg6on4/upload/e2b025e94f4ae29d131bd6081cad013d.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Improve Your Kotlin Code: Tips for Effective RegEx Usage"
seoDescription: "Optimize Kotlin code using regular expressions for cleaner, readable, maintainable code. Learn best practices and proper RegEx usage with examples."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

*RegEx is a wonderful tool, we should use them carefully and not look smart.*

## Problem

* Readability
    
* Maintainability
    
* Testability
    
* Intention Revealing
    

## Solution

1. Use regular expressions just for string validation.
    
2. If you need to manipulate objects, don't make them strings.
    

## Sample Code

### Wrong

```kotlin
val regex = "^\\+(?:[0-9a-zA-Z][– -]?){6,14}[0-9a-zA-Z]$".toRegex()
```

### Right

```kotlin
val prefix = """\+"""
val digit = "[0-9a-zA-Z]"
val space = "[– -]"
val phoneRegex = "^$prefix(?:$digit$space?){6,14}$digit$".toRegex()
```

## Conclusion

Regular expressions are a great tool for string validation. We must use them in a declarative way and just for strings.

Names are very important to understand pattern meanings.

If we need to manipulate objects or hierarchies, we should do it in an *object way*.

Unless we have a conclusive benchmark of **impressive** performance improvement.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 41 - Regular Expression Abusers](https://maximilianocontieri.com/code-smell-41-regular-expression-abusers) by @[Maxi Contieri](@mcsee)
