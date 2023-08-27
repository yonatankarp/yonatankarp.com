---
title: "Create a Custom Spring Boot App Banner"
seoTitle: "Customize Spring Boot Banner: Personalize Easily"
seoDescription: "Learn how to easily customize your Spring Boot app banner with custom text or images in just minutes. Personalize your app's look and feel!"
datePublished: Sat Dec 03 2022 08:10:08 GMT+0000 (Coordinated Universal Time)
cuid: clb7nmri0000408jqfuvz6py4
slug: create-a-custom-spring-boot-app-banner
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/Dnkr_lmdKi8/upload/v1669959141692/4QitOw44k.jpeg
tags: spring, guide, springboot, customization

---


Today, I'd like to share a fun feature of Spring with you. It's not new or crucial, but it's definitely enjoyable!

Normally, when you launch your Spring Boot app, you see a standard banner in the logs like this:

![Spring Boot Banner](https://cdn-images-1.medium.com/max/1600/1*qhCPGhoCfVHDiV_8C2w-IQ.png align="left")

But have you ever thought about replacing that Spring banner with your own custom banner? If you have, then this article is for you!

To begin, let's visit [https://patorjk.com/](https://patorjk.com/), a website that helps us create custom text. You can enter the desired text for your log and choose from a variety of fonts. For example:

![Custom Text](https://cdn-images-1.medium.com/max/1600/1*C4UCtmMxeYdyA3adBhwwEg.png align="left")

You can also click the **Test All** button to explore all the available options.

Now, let's copy our text and place it inside the application under `/resources/banner.txt`, as shown below:

![Copy Custom Text](https://cdn-images-1.medium.com/max/1600/1*e0zWvbSOc4-txfoe9I1Q5Q.png align="left")

Next, let's relaunch our app:

![Custom Banner](https://cdn-images-1.medium.com/max/1600/1*Nm-03wgAZnZGCV-Z2WBmFQ.png align="left")

As you can see, our custom banner has been successfully replaced! Great job! :)

If you're using Spring Boot 2, you can even convert images into [ASCII art](https://en.wikipedia.org/wiki/ASCII_art). However, please note that this functionality is deprecated and has been removed in Spring Boot `3.0.0`, which was released recently.

To convert images into ASCII art, follow these steps:

* Add your image to the resources directory. For this article, we'll use the following logo:
    

![Logo](https://cdn-images-1.medium.com/max/1600/1*nF3paal9R_NCqIpeMEg_lA.png align="left")

* Add the following configuration to your [`application.properties`](http://application.properties) or `application.yml` file to specify the location of your image:
    

```yaml
spring:
  banner:
    image:
      location: sumup.png
      height: 20 # can be adjusted to resize your banner
```

* Make sure there's no `banner.txt` file in your resource directory.
    

Let's run our app again...

![Custom Logo](https://cdn-images-1.medium.com/max/1600/1*Se9si_zINQyzFsUTQTmvSg.png align="left")

As you can see, our new logo magically appears!

### Summary

In this short article, I've shown you how to customize your Spring banner using text or even images (for Spring Boot `2.x.x`) in just a few minutes.

---

Stay updated with my latest thoughts and ideas by registering for my [newsletter](https://yonatankarp.com/newsletter). Connect with me on [LinkedIn](https://www.linkedin.com/in/yonatankarp/) or [Twitter](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!
