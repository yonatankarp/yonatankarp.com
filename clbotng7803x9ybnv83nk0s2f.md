# Kotlin Code Smells 010 - Null

> ***TL;DR:*** Null does not exist in the real world. His creator regrets it, and programmers around the world suffer from it. Don't be a part of it.

Programmers use `null` for different flags. It can hint at an absence, an undefined value, an error, etc. Multiple semantics lead to coupling and errors.

# Problems

*   Coupling among the callers and the senders.
    
*   Mismatch among the callers and the senders.
    
*   `if`/`when` Polluting.
    
*   Null is not polymorphic with real objects. Hence, *Null Pointer Exception*
    
*   Null does not exist in the real world. Thus, it violates [**Bijection Principle**](https://maximilianocontieri.com/the-one-and-only-software-design-principle)
    

# Solutions

*   Avoid using nullable types when possible.
    
*   Use the [**NullObject pattern**](https://en.wikipedia.org/wiki/Null_object_pattern) to avoid ifs.
    

# Exceptions

*   APIs, Databases, and external systems where `null` does exist.
    

# **Sample Code**

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
    fun total() = discountCoupon.discount(subtotal());
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

# Conclusion

*   `null` is the billion-dollar mistake. Yet, most program languages support them and libraries suggest their usage. Always prefer not to use nullable types in Kotlin unless absolutely necessary, and use the NullObject pattern to represent the absence of a field if needed.
    

# More info

*   [**Null: The Billion-Dollar Mistake**](https://maximilianocontieri.com/null-the-billion-dollar-mistake)
    
*   [Null safety in Kotlin](https://kotlinlang.org/docs/null-safety.html)
    

# Credits

*   [Code Smell 12 - Null](https://maximilianocontieri.com/code-smell-12-null) by @[Maxi Contieri](@mcsee)