---
title: "Kotlin Code Smell 25 - State as Properties"
seoTitle: "Kotlin Best Practices: Avoiding State as Properties Pitfalls"
seoDescription: "Discover how to improve your Kotlin code quality by addressing the common issue of state as properties, learn the best practices, and avoid potential pitfal"
datePublished: Fri Aug 04 2023 08:30:08 GMT+0000 (Coordinated Universal Time)
cuid: clkwbscpj000909jyarzf23mi
slug: kotlin-code-smell-25-state-as-properties
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/SP5odY8jGLA/upload/392baf1f709148ceaa78c12c4fc45bee.jpeg
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1

---



When an object changes its state, the best solution is to modify the attribute, right?

## Problems

* Mutability
    
* Attribute Polluting
    
* Setters
    

## Solutions

1. Use immutable objects
    
2. The model states as mathematical set inclusion.
    
3. Separate the state from the object as it is accidental.
    

## Examples

* State diagrams
    

## Sample Code

### Wrong

```kotlin
sealed class OrderState
data object OrderStatePending : OrderState()
data object OrderStateConfirmed: OrderState()

class Order(private val items: List<Int>) {

    private var state: OrderState = OrderStatePending

    fun changeState(newState: OrderState) {
        state = newState
    }
}
```

### Right

```kotlin
sealed class OrderState
data object OrderStatePending : OrderState()
data object OrderStateConfirmed: OrderState()

data class Order(val items: List<Int>, val state: OrderState)

val items = listOf(1, 2, 3)
val pendingOrder = Order(items, OrderStatePending)
val confirmedOrder = pendingOrder.copy(state = OrderStateConfirmed)
```

## Exceptions

* Over Design
    
* Performance issues (if a serious benchmark supports it).
    

## Conclusion

This technique is elegant but can lead to overdesign. For example, changing a visual component's color should be a counterexample of this smell.

We should be aware and very cautious as with any other smell.

These are hints and not rigid rules.

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 35 - State as Properties](https://maximilianocontieri.com/code-smell-35-state-as-properties) by @[Maxi Contieri](@mcsee)
