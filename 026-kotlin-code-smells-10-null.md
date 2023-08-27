---
title: "Kotlin Code Smell 10 - Null"
subtitle: "Embracing Existence: Say No to Null"
slug: kotlin-code-smell-10-null
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, kotlin-beginner, code-smell-1, null-safety
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1670059688939/qyf3lGeJB.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Kotlin Code Smells: Avoiding Null and Embracing NullObject Pattern"
seoDescription: "Discover the drawbacks of using null in Kotlin programming and learn how to avoid them with the NullObject pattern. Improve code quality and prevent errors."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR:** Null does not exist in the real world. Its creator regrets it, and programmers worldwide suffer from it. Avoid being a part of it.

Programmers often use `null` to represent various conditions such as absence, undefined values, or errors. However, this leads to coupling and errors due to multiple interpretations.

## Problems

* Coupling between callers and senders.
    
* Mismatch between callers and senders.
    
* Pollution with `if`/`when` statements.
    
* Null is not polymorphic with real objects, resulting in Null Pointer Exceptions.
    
* Null does not exist in the real world, violating the [Bijection Principle](https://maximilianocontieri.com/the-one-and-only-software-design-principle).
    

## Solutions

* Avoid using nullable types whenever possible.
    
* Use the [NullObject pattern](https://en.wikipedia.org/wiki/Null_object_pattern) to eliminate conditional statements.
    

## Exceptions

* APIs, databases, and external systems where `null` does exist.
    

## Sample Code

### Wrong

```kotlin
class CartItem(val price: Double)

class DiscountCoupon(val rate: Double)

class Cart(
    private val items: List<CartItem>,
    private val discountCoupon: DiscountCoupon?
) {
    fun subtotal() = 
        items
            .fold(0.0) { acc, next -> acc + next.price }
    fun total() =
        discountCoupon
            ?.let {
                subtotal() * (1 - discountCoupon.rate)
            } ?: subtotal()
}

fun main() {
    val cartItems = listOf(
        CartItem(1.0),
        CartItem(2.0),
        CartItem(7.0)
    )

    var cart = Cart(cartItems, DiscountCoupon(0.15))
    println(cart.total()) // 10 - 1.5 = 8.5

    cart = Cart(cartItems, null);
    println(cart.total()) // 10 - null  = 10
}
```

### Right

```kotlin
class CartItem(val price: Double)

interface Coupon {
    fun discount(subtotal: Double): Double
}

class DiscountCoupon(private val rate: Double) : Coupon {
    override fun discount(subtotal: Double) = subtotal * (1 - rate)
}

class NullCoupon : Coupon {
    override fun discount(subtotal: Double) = subtotal
}

// Notice that we're not using a nullable type for Coupon anymore!
class Cart(
    private val items: List<CartItem>,
    private val discountCoupon: Coupon
) {
    fun subtotal() =
        items
            .fold(0.0) { acc, next -> acc + next.price }
    fun total() = discountCoupon.discount(subtotal())
}

fun main() {
    val cartItems = listOf(
        CartItem(1.0),
        CartItem(2.0),
        CartItem(7.0)
    )

    var cart = Cart(cartItems, DiscountCoupon(0.15))
    println(cart.total()) // 10 - 1.5 = 8.5

    cart = Cart(cartItems, NullCoupon())
    println(cart.total()) // 10 - nullObject  = 10
}
```

## Conclusion

* `null` is often considered a billion-dollar mistake. Despite that, many programming languages support its usage, and libraries even encourage it. In Kotlin, it's recommended to avoid nullable types unless absolutely necessary, and if needed, utilize the NullObject pattern to represent the absence of a field.
    

---

Stay updated with my latest thoughts and ideas by registering for my [**newsletter**](https://yonatankarp.com/newsletter). Connect with me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## More info

* [**Null: The Billion-Dollar Mistake**](https://maximilianocontieri.com/null-the-billion-dollar-mistake)
    
* [Null safety in Kotlin](https://kotlinlang.org/docs/null-safety.html)
    

## Credits

* [Code Smell 12 - Null](https://maximilianocontieri.com/code-smell-12-null) by @[Maxi Contieri⭐⭐⭐](@mcsee)
