---
title: "Kotlin Code Smell 3 - String Abusers"
seoTitle: "Avoid String Abuse in Kotlin"
seoDescription: "Improve code complexity, readability, and maintainability in Kotlin by using real objects instead of excessive string manipulation."
datePublished: Fri Dec 02 2022 08:00:42 GMT+0000 (Coordinated Universal Time)
cuid: clb67usah012a2knv0yjc0pfr
slug: kotlin-code-smell-3-string-abusers
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/C5SUkYZT7nU/upload/v1669832838326/gSAr6Ry6C.jpeg
tags: programming-blogs, kotlin, clean-code, strings, kotlin-beginner

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