---
title: "Kotlin Code Smell 12 - Ripple Effect"
subtitle: "When Ripples Become Waves: The Importance of Decoupling"
slug: kotlin-code-smell-12-ripple-effect
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: refactoring, programming-blogs, kotlin, clean-code, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670069003678/yc3eAr775.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Decoupling Legacy Systems: Addressing the Ripple Effect"
seoDescription: "Learn how to decouple legacy systems to avoid the ripple effect. Covering strategies, refactoring, and interface dependencies for better system management."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** If small changes have a significant impact, you need to decouple your system.

# Problems

* Coupling
    

# Solutions

* Decouple.
    
* Cover with tests.
    
* Refactor and isolate what is changing.
    
* Depend on interfaces.
    

# Examples

* Legacy Systems
    

# Sample Code

### Wrong

```kotlin
class Time(
    private val hour: Int,
    private val minute: Int,
    private val seconds: Int
) {
    fun now() {
        // call operating system  
    } 
}

// Adding a TimeZone will have a big Ripple Effect
// Changing now() to consider timezone will also bring the effect
```

### Right

```kotlin
// Removed now() since it is invalid without context
data class Time(
    private val hour: Int,
    private val minute: Int,
    private val seconds: Int,
    private val timezone: String
)

class RelativeClock(private val timezone: String) {
    fun now() {
        val localSystemTime = this.localSystemTime()
        val localSystemTimezone = this.localSystemTimezone()
        
        // Make some calculations to translate timezones...
        
        return Time(..., timezone)
    }
    
    // ...
}
```

# Conclusion

There are multiple strategies to deal with legacy and coupled systems. It is essential to address this problem before it becomes unmanageable.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

# More info

* [**How to Decouple a Legacy System**](https://maximilianocontieri.com/how-to-decouple-a-legacy-system)
    

# Credits

* [Code Smell 16 - Ripple Effect](https://maximilianocontieri.com/code-smell-16-ripple-effect) by @[Maxi Contieri⭐⭐⭐](@mcsee)
