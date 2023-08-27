---
title: "Kotlin Code Smell 24 - Tackling Too Many Attributes"
subtitle: "Trimming the Attribute Fat"
slug: kotlin-code-smell-24-tackling-too-many-attributes
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/kG71BXh8KFw/upload/42c77ff04e136ab3fe5dabad1f83a299.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Mastering Kotlin: How to Tackle the Too Many Attributes Code Smell"
seoDescription: "Refine your Kotlin code by resolving attribute overload, enhancing code cohesion, maintainability, and readability for a more effective programming experience"
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---


## Problem

* Low Cohesion
    
* Coupling
    
* Maintainability
    
* Readability
    

## Solution

1. Identify methods related to specific groups of attributes.
    
2. Cluster these methods together.
    
3. Break down the original class into smaller, more focused objects based on these clusters.
    
4. Replace existing references with new objects.
    

## Examples

\- DTOs

\- Denormalized table rows

## Sample Code

### Wrong

```kotlin
class ExcelSheet (
          val filename: String,
          val fileEncoding: String,
          val documentOwner: String,
          val documentReadPassword: String,
          val documentWritePassword: String,
          val creationTime: LocalDateTime,
          val updateTime: LocalDateTime,
          val revisionVersion: String,
          val revisionOwner: String,
          val previousVersions: List<String>,
          val documentLanguage: String,
          val cells: List<Cell>,
          val cellNames: List<String>,
          val geometricShapes: List<Shape>,
)
```

### Right

```kotlin
class ExcelSheet (
          val fileProperties: FileProperties,
          val securityProperties: SecurityProperties,
          val datingProperties: DocumentDatingProperties,
          val revisionProperties: RevisionProperties,
          val languageProperties: LanguageProperties,
          val content: DocumentContent,

)

// The object now has fewer attributes, resulting in improved
// testability.
// The new objects are more cohesive, more testable, and lead to fewer
// conflicts, making them more reusable. Both FileProperties and
// SecurityProperties can be reused for other documents. Additionally,
// rules and preconditions previously found in fileProperties will be
// relocated to this object, resulting in a cleaner ExcelSheet
// constructor.
```

## Conclusion

Bloated objects know too much and are very difficult to change due to cohesion.

Developers change these objects a lot, so they bring merge conflicts and are a common problem source.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 34 - Too Many Attributes](https://maximilianocontieri.com/code-smell-34-too-many-attributes) by @[Maximiliano Contieri](@mcsee)
