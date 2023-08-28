---
title: "Kotlin Code Smell 21 - Mocking Business"
subtitle: "Mockumentary: When Your Test Takes Acting Too Far"
slug: kotlin-code-smell-21-mocking-business
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, clean-code, kotlin-beginner, code-smell-1
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ZLe2BnUZuKA/upload/c629759eea86662fe138045c0357ba87.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Code Smell 21 - Mocking Business: Best Practices and Solutions"
seoDescription: "Learn how to avoid code complexity and false security by mocking non-business entities. Find best practices and solutions for maintainable tests."
seriesSlug: kotlin-code-smells
# Table of contents
enableToc: true
saveAsDraft: false
---

## Problem

* Complexity
    
* False sense of security.
    
* Parallel/Paired objects (real and mocks) can lead to maintainability issues.
    
* Maintainability
    

## Solution

* Mock just non-business entities.
    
* Remove the mock if its interface has too much behavior.
    

## Sample Code

### Wrong

```kotlin
class PaymentTest {
    @Test
    fun `process payment should return true on successful payment`() {
        val paymentDetails = mapOf(
            "amount" to "100",
            "currency" to "USD",
            "cardNumber" to "1234567890123456",
            "expiryDate" to "12/20",
            "cvv" to "123"
        )

        // We should not mock a business object
        val payment = mockk<Payment>()
        every { payment.process(any(), any()) } returns true

        // This is an external and coupled system.
        // We have no control on it so tests might be fragile
        val authorizeNet = AuthorizeNetAIM(Payment.API_ID, Payment.TRANSACTION_KEY)

        val result = payment.process(authorizeNet, paymentDetails)
        assertTrue(result)
    }
}
```

### Right

```kotlin
class PaymentTest {
    @Test
    fun `process payment should return true on successful payment`() {
        val paymentDetails = mapOf(
            "amount" to "100",
            "currency" to "USD",
            "cardNumber" to "1234567890123456",
            "expiryDate" to "12/20",
            "cvv" to "123"
        )

        val payment = Payment()

        // External system is mocked
        val response = Response(approved = true, transactionId = "1234567890")
        val authorizeNet = mockk<AuthorizeNetAIM>()
        every { authorizeNet.authorizeAndCapture() } returns response

        val result = payment.process(authorizeNet, paymentDetails)
        assertTrue(result)
    }
}
```

## Exceptions

* Mocking accidental problems (serialization, databases, APIs) is a very good habit to avoid coupling.
    

## Conclusion

Mocks, like other test doubles, are valuable tools. Using them judiciously is an art.

Imagine a play in which each actor, instead of rehearsing with other actors, had to interact with 25 scriptwriters. The actors would never rehearse together. How would the result of the play be?

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!

---

## Credits

* [Code Smell 30 - Mocking Business](https://maximilianocontieri.com/code-smell-30-mocking-business) by @[Maximiliano Contieri](@mcsee)
