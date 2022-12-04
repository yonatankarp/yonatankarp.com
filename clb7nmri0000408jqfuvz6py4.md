# Create your custom Spring Boot app banner

Today I would like to show you a feature of Spring that is not new, nor important, but it’s just fun!

Usually, when launching your Spring Boot app, you would see in the logs something like that:

![](https://cdn-images-1.medium.com/max/1600/1*qhCPGhoCfVHDiV_8C2w-IQ.png align="left")

However, have you ever considered replacing the Spring banner in the logs with your custom banner? if so, follow this article!

To start, let’s visit the website [https://patorjk.com/](https://patorjk.com/) which will help us build our custom text. You can enter the text you want to see in your log and select the font out of tons of possible fonts. For example:

![](https://cdn-images-1.medium.com/max/1600/1*C4UCtmMxeYdyA3adBhwwEg.png align="left")

You can also easily clock the **Test All** button to see all possible options

Now, let’s copy our text, and put it inside the application under the `/resources/banner.txt` as follows:

![](https://cdn-images-1.medium.com/max/1600/1*e0zWvbSOc4-txfoe9I1Q5Q.png align="left")

Now let’s launch our app again:

![](https://cdn-images-1.medium.com/max/1600/1*Nm-03wgAZnZGCV-Z2WBmFQ.png align="left")

As you can see, our banner was indeed replaced! Great job! :)

If you’re using Spring Boot 2, you can also convert images into [ASCII art](https://en.wikipedia.org/wiki/ASCII_art) — however, this functionality is deprecated and was removed in Spring Boot 3.0.0 which was just released this week.

To do so, follow the steps:

*   Add your image to the resources directory. For this article we will use this logo:
    
    ![](https://cdn-images-1.medium.com/max/1600/1*nF3paal9R_NCqIpeMEg_lA.png align="left")
    
*   Add to your `application.properties` or `application.yml`file the following configuration to locate your image:
    

```yaml
spring:
  banner:
    image:
      location: sumup.png
      height: 20 # can be used to alter your banner size
```

*   Ensure that you don’t have any `banner.txt` in your resource directory
    

Let’s run our app again…

![](https://cdn-images-1.medium.com/max/1600/1*Se9si_zINQyzFsUTQTmvSg.png align="left")

As you can see, our new logo appeared like magic :)

### Summary

In this short article, I have shown you how you can customize your Spring banner using text, or even images (if you’re running on Spring boot `2.x.x`) in a couple of minutes.