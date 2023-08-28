---
title: "Build your own Twitter Bot with Kotlin"
subtitle: "Learn how to create a Twitter bot using Kotlin. Follow this step-by-step guide to build your own bot and automate your tweets. No coding experience required"
slug: build-your-own-twitter-bot-with-kotlin
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: twitter, guide, kotlin, bots, kotlin-beginner
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1639603533753/qMyvtIuf6.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "Build Your Own Twitter Bot with Kotlin | Step-by-Step Guide"
seoDescription: "Learn how to create a Twitter bot using Kotlin. Follow this step-by-step guide to build your own bot and automate your tweets. No coding experience required"
seriesSlug:
# Table of contents
enableToc: false
saveAsDraft: false
---

I read an excellent article by [Martin Erlic](https://medium.com/u/f864d52751a7) called "[How To Make a Scary Russian Twitter Bot With Java](https://medium.com/@SeloSlav/how-to-make-a-scary-russian-twitter-bot-with-java-b7b62768a3ac)" and I wanted to update it using Kotlin.

Unlike the original article, I will use Kotlin, a newer and more modern JVM-based language, and Gradle as the build system.

To build the bot, follow these simple steps:

### Step 1 — Create a Twitter Application

The first step is to create a [Twitter Application](https://apps.twitter.com/) using the user account you want to perform the API requests with. By creating the Twitter application, you will obtain the API key and API secret, which we'll later use to authenticate our bot with Twitter.

1. Go to the dashboard and click on "Create App". Choose a unique name for your bot.
    
    ![name your app](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485716829/e4DDWaddA.png align="left")
    

![get account keys](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485718928/iotlNGKrr.png align="left")

2\. Change the token access from `Read only` to `Read and Write` in the dashboard.

![change access to read & write](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485720623/DJ_XtxBF4.png align="left")

3\. Create the access token and access secret.

![create access keys](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485727232/JeHY2mZsT.png align="left")

4\. Remember to store all the keys as we'll need them in Step 4!

### Step 2 — Clone the Project Starter from GitHub

To save you time setting up the project, I've created a starter version on GitHub that contains all the necessary setups. You can clone it from [https://github.com/yonatankarp/twitter-bot](https://github.com/yonatankarp/twitter-bot). You can use IntelliJ Community Edition or other tools like Visual Studio Code.

### Step 3 — Prepare Tweets and Configure Twitter4J

In the **resources** directory, open the **tweets.txt** file. This file will contain the tweets that our bot will publish. Each tweet should be on a separate line. Aim for at least 100 unique tweets to reduce the chances of content detection. Remember the 280 character limit imposed by Twitter.

Open the [**twitter4j.properties**](http://twitter4j.properties) file in the same resources directory. Fill in the following information from **Step 1**:

```text
debug=true  
oauth.consumerKey=  
oauth.consumerSecret=  
oauth.accessToken=  
oauth.accessTokenSecret=
```

With the authentication set, we can move on to the code!

### Step 4 — The Code

Create a new Kotlin file called `TwitterBot.kt` in the `src/main/kotlin` directory. Replace the existing code with the following:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485728903/USq7hjFK9.png align="left")

```kotlin
package com.example

import twitter4j.Status
import twitter4j.TwitterFactory

object TwitterBot {
    @JvmStatic
    fun main(args: Array<String>) {
        tweetAll()
    }

    /**
     * Reads all tweets from the `tweets.txt` file in the resources
     * directory and tweets them one by one with a delay between each.
     */
    private fun tweetAll() {
        val tweets = getResourceAsLines("tweets.txt")
        tweets.forEach { tweet ->
            sendTweet(tweet)
            println("Tweeting: $tweet...")
            waitUntilNextTweet()
        }
    }

    /**
     * Reads the content of a given file.
     *
     * @param resource the path under the resources directory
     *        to read from.
     * @return a list of strings representing each line in the file.
     */
    private fun getResourceAsLines(resource: String): List<String> =
        TwitterBot.javaClass.classLoader
            .getResource(resource)
            .readText()
            .lines()

    /**
     * Publishes a given tweet to the Twitter account.
     */
    private fun sendTweet(tweet: String) = kotlin.runCatching {
        TwitterFactory.getSingleton().updateStatus(tweet)
    }
        .onSuccess { status: Status -> println(status) }
        .onFailure { it.printStackTrace() }

    /**
     * Waits for a constant amount of time between every two
     * tweets to be published.
     *
     * @param delayInSeconds the number of minutes to delay,
     * 30 minutes by default.
     */
    private fun waitUntilNextTweet(delayInSeconds: Long = 30) =
        kotlin.runCatching {
            println("Sleeping for $delayInSeconds minutes...")
            // Change here to increase or decrease the delay.
            Thread.sleep(delayInSeconds * 60000)
        }
            .onFailure { it.printStackTrace() }
}
```

The code above is relatively simple and well-documented to ensure ease of understanding.

As mentioned in the original article, make sure to familiarize yourself with the [Twitter API Limits](https://help.twitter.com/en/rules-and-policies/twitter-limits) to understand the frequency of tweeting without encountering issues.

### Step 5 — Running the Application

You can run the program from the command line in the project directory using the following command:

```shell
./gradlew clean build && java -jar ./build/libs/twitter-bot-1.0-SNAPSHOT.jar
```

If everything is successful, you should see the following output in your debug terminal:

![bot output](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485730383/_IXLpBWjd.png align="left")

Congratulations! Your bot is now functional!

The completed bot is available in the repository mentioned in Step 2, under the branch name `complete_bot` at [https://github.com/yonatankarp/twitter-bot/tree/complete\_bot](https://github.com/yonatankarp/twitter-bot/tree/complete_bot).

%%[contact-me]
