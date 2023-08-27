---
title: "Kotlin Code Smell 18 - Arrays Abusers"
subtitle: "Beyond Arrays: Designing with First-Class Objects"
slug: kotlin-code-smell-18-arrays-abusers
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1671275286483/KSafVuVqm.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Reifying Objects: Solving Common Software Problems"
seoDescription: "Learn how reifying objects can solve common software problems such as coupling, information hiding, and code duplication. Improve your software design today"
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> ***TL;DR:*** *Use arrays for rapid prototyping, use object for serious business.*

## **Problems**

* Coupling
    
* Information Hiding
    
* Code Duplication
    
* Fail Fast
    
* Integrity
    

## Solutions

1. Reify objects
    
2. Create cohesive small objects
    
3. Avoid anemic objects and identify their cohesive relations.
    

## Sample Code

### Wrong

```kotlin
// Array with some raw data, what can go wrong?
val coordinates = arrayOf(1000.0, 2000.0)
```

### Anemic

```kotlin
data class GeographicCoordinate(
    val longitude: Double,
    val latitude: Double 
)

// An error should be thrown since these values don't exist on Earth
val coordinates = GeographicCoordinate(1000.0, 2000.0)
```

### Validated

```kotlin
class GeographicCoordinate(
    val longitude: Double,
    val latitude: Double
) {
    init {
        if (!isValidLatitude(latitude))
            throw InvalidLatitudeException(latitude)
        if (!isValidLongitude(longitude))
            throw InvalidLongitudeException(longitude)
    }

    private fun isValidLatitude(latitude: Double) =
        latitude in -90.0..90.0
    private fun isValidLongitude(longitude: Double) =
        longitude in -180.0..180.0
}

// An error should be thrown since these values don't exist on Earth
val coordinates = GeographicCoordinate(1000.0, 2000.0)
```

### Right

Degrees deserve reification...

```kotlin
data class Latitude(val degree: Double) {
    init {
        if (degree !in -90.0..90.0)
            throw InvalidLatitudeException(degree)
    }
}

data class Longitude(val degree: Double) {
    init {
        if (degree !in -180.0..180.0)
            throw InvalidLongitudeException(degree)
    }
}

data class GeographicCoordinate(
    val longitude: Longitude,
    val latitude: Latitude
)

// An error should be thrown since these values don't exist on Earth
val coordinates = GeographicCoordinate(
    Longitude(1000.0),
    Latitude(2000.0)
)
```

Many people suffer from **primitive obsession** and believe this is over-design. Designing software is about making decisions and comparing trade-offs. The performance argument is not valid nowadays since modern virtual machines can efficiently deal with small short-lived objects.

## **Conclusion**

When creating objects, we must not think of them as *data*. This is a common misconception.

We should stay loyal to our [**Bijection**](https://maximilianocontieri.com/the-one-and-only-software-design-principle) and discover real-world objects.

Most associative arrays have cohesion and represent real-world entities, and we must treat them as first-class objects.

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 27 - Associative Arrays](https://maximilianocontieri.com/code-smell-27-associative-arrays) by @[Maxi Contieri](@mcsee)
