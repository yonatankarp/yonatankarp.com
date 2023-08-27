---
title: "Kotlin Code Smell 3 - String Abusers"
subtitle: ""
slug: kotlin-code-smell-3-string-abusers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, strings, kotlin-beginner
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/C5SUkYZT7nU/upload/v1669832838326/gSAr6Ry6C.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Avoid String Abuse in Kotlin"
seoDescription: "Improve code complexity, readability, and maintainability in Kotlin by using real objects instead of excessive string manipulation."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Use actual objects and abstractions instead of random string manipulation.

## Problems

* Complexity
    
* Readability
    
* Maintainability
    
* Lack of Abstractions
    

## Solutions

* Utilize objects instead of strings.
    
* Replace strings with data structures that handle object relations.
    
* Identify bijection problems between real objects and strings.
    

## Examples

* Serializers
    
* Parsers
    

## Sample Code

### Wrong

```kotlin
val schoolDescription = "College of Springfield"

// location = "Springfield"
val location = """[^ ]*\$""".toRegex()
    .find(schoolDescription)?.value

// school = "College"
val school = """^[\w]+""".toRegex()
    .find(schoolDescription)?.value
```

### Right

```kotlin
class School(
    private val name: String,
    private val location: Location
) {
    fun description() = "$name of ${location.name}"
}

class Location(
    val name: String
)
```

## Conclusion

Avoid excessive reliance on strings. Prioritize actual objects. Establish clear protocols to differentiate them from strings.

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 04 - String Abusers](https://maximilianocontieri.com/code-smell-04-string-abusers) by @[Maxi Contieri⭐⭐⭐](@mcsee)
