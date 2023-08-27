---
title: "Kotlin Code Smell 33 - Caches"
subtitle: ""
slug: kotlin-code-smell-33-caches
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/gRujUd2CtTk/upload/e222cdbad8f7b3fb3b7d41e6e5083c29.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Enhance Your Kotlin Apps: A Guide to Smart Caching Solutions"
seoDescription: "Uncover caching challenges and solutions, focusing on coupling, maintainability, and effective cache management techniques."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


> TL;DR - Caches discusses the problems with caching, such as coupling and maintainability, and proposes solutions like using an object mediator, testing invalidation scenarios, and modeling real-world cache metaphors. It concludes that caches should be functional, and intelligent, and domain objects shouldn't be cached.

## Problem

* Coupling
    
* Testability
    
* Cache Invalidation
    
* Maintainability
    
* Premature Optimization
    
* Erratic Behavior
    
* Lack of Transparency
    
* Non-Deterministic Behavior
    

## Solution

1. If you have a conclusive benchmark and are willing to accept some coupling, put an object in the middle.
    
2. Unit test all your invalidation scenarios. Experience shows that we face them in an incremental way.
    
3. Look for a real-world cache metaphor and model it.
    

## Sample Code

### Wrong

```kotlin
typealias Cache<T> = MutableMap<String, List<T>>

class Book(
    private val cachedBooks: Cache<Book> = mutableMapOf()
) {
    fun getBooks(title: String): List<Book> {
        return if (cachedBooks.containsKey(title)) {
            cachedBooks[title]!!
        } else {
            val booksFromDatabase = getBooksFromDatabase(title)
            cachedBooks[title] = booksFromDatabase
            booksFromDatabase
        }
    }

    private fun getBooksFromDatabase(title: String): List<Book> =
        globalDatabase().selectFrom("Books", "WHERE TITLE = $title")
}
```

### Right

```kotlin
typealias Cache<T> = MutableMap<String, T>

interface BookRetriever {
    fun bookByTitle(title: String): Book?
}

class Book {
    // Just Book-related Stuff
}

class DatabaseLibrarian : BookRetriever {
    // Go to the database (not global hopefully)
    override fun bookByTitle(title: String): Book? { ... }
}

// We always look for real-life metaphors
class HotSpotLibrarian(
    private val inbox: Inbox,
    private val realRetriever: BookRetriever
) : BookRetriever {
    override fun bookByTitle(title: String): Book? {
        return if (inbox.includesTitle(title)) {
            // We are lucky. Someone has just returned the book copy.
            inbox.retrieveAndRemove(title)
        } else {
            realRetriever.bookByTitle(title)
        }
    }
}

class Inbox(private val books: Cache<Book> = mutableMapOf()) {   
    fun includesTitle(title: String) { ... }
    fun retrieveAndRemove(title: String): Book? { ... }
    fun addBook(title: String, book: Book) { ... }
}
```

## Conclusion

Caches should be functional and intelligent, allowing for effective management of invalidation. General-purpose caches are best suited for low-level objects like operating systems, files, and streams, while domain objects should not be cached.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 49 - Caches](https://maximilianocontieri.com/code-smell-49-caches) by @[Maximiliano Contieri](@mcsee)
