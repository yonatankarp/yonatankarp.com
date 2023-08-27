---
title: "Kotlin Code Smell 5 - Too Clever For Your Own Good"
seoTitle: "Clean Code: Avoid Being Too Clever"
seoDescription: "Learn why being too clever in your code can hinder readability and maintainability. Discover the benefits of clean code practices for better development."
datePublished: Mon Dec 05 2022 07:30:42 GMT+0000 (Coordinated Universal Time)
cuid: clbah3rih0ba1ranvde827icw
slug: kotlin-code-smell-5-too-clever-for-your-own-good
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/4Ennrbj1svk/upload/v1669912664560/pKnEMFCe19.jpeg
tags: programming-blogs, kotlin, kotlin-beginner, code-smell-1

---


> **TL;DR:** Don't pretend you are too smart. Clean code requires readability and simplicity.

## Problems

* Readability
    
* Maintainability
    
* Code quality
    
* Premature optimization
    

## Solutions

* Refactor the code
    
* Use [**better names**](https://maximilianocontieri.com/what-exactly-is-a-name-part-i-the-quest)
    

## Examples

* Optimized loops
    

## Exceptions

* Optimized code for low-level operations.
    
* 1-liner code functions
    

## Sample Code

### Wrong

```kotlin
fun primeFactors(n: Int): ArrayList<Int> {
    val f = ArrayList<Int>()
    var d = 2
    var nn = n

    while (nn >= 2) {
        if (nn % d == 0) {
            f.add(d)
            nn /= d
        } else {
            d++
        }
    }

    return f
}
```

### Right

```kotlin
fun primeFactors(numberToFactor: Int): ArrayList<Int> {
    val factors = ArrayList<Int>()
    var divisor = 2
    var remainder = numberToFactor

    while (remainder >= 2) {
        if (remainder % divisor == 0) {
            factors.add(divisor)
            remainder /= divisor
        } else {
            divisor++
        }
    }
    return factors
}
```

## Conclusion

Developers who are too clever for their own good write cryptic code to brag. Smart developers write clean code. Clear beats clever!

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 06 - Too Clever Programmer](https://maximilianocontieri.com/code-smell-06-too-clever-programmer) by @[Maxi Contieri⭐⭐⭐](@mcsee)
