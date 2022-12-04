# Build your own Twitter Bot with Kotlin

I’ve read the extremely nice article made by [Martin Erlic](https://medium.com/u/f864d52751a7) “[How To Make a Scary Russian Twitter Bot With Java](https://medium.com/@SeloSlav/how-to-make-a-scary-russian-twitter-bot-with-java-b7b62768a3ac)“ and I wanted to update it a bit using Kotlin.

Unlike the original article, I would use Kotlin (as it’s a newer and more modern JVM-based language) and Gradle as the build system.

I would build the bot in a few simple steps:

### Step 1 — Make a Twitter Application

The first step is to make a [Twitter Application](https://apps.twitter.com/) with the user account that you want to perform the API request with. Creating the Twitter application will give you the API key and API secret that we’ll later use to authenticate with Twitter with our bot.

### Create the application

In the dashboard, go to “Create App” and choose a name for your bot (the name must be unique)

![name your app](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485716829/e4DDWaddA.png)

![get account keys](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485718928/iotlNGKrr.png)

Go back to the dashboard and change your token access from `Read only` to `Read and Writer`

![change access to read & write](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485720623/DJ_XtxBF4.png)

Now all that is left is to create the access token and access secret:

![create access keys](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485727232/JeHY2mZsT.png)

Make sure to store all keys as we’ll use them in step 4!

### Step 2 — Clone the project starter from GitHub

To help you skip the setup of the project, I’ve created a starter version of the project that you can clone from GitHub that contains all the required setup for our project. You can easily clone it from here: [https://github.com/yonatankarp/twitter-bot](https://github.com/yonatankarp/twitter-bot).

During this article, I would use [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/#section=mac) as it works very nicely with Kotlin, but other tools such as [Visual Studio Code](https://code.visualstudio.com/) will also do the trick for you.

### Step 3 — Prepare Tweets and Configure Twitter4J

In your **resources** directory, open the **tweets.txt** file. This file will contain the tweets that our bot will publish separated line by line until there are no more tweets to consume. Note that the more unique your content is the less likely your bot will be detected due to content detection. I would stick with the original recommendation of at least 100 unique tweets. Please also remember the 280 characters max length limit set by Tweeter.

In the same resources directory, open the file **twitter4j.properties**. You will need to fill in the following information from **Step 1** of this tutorial:

```text
debug=true  
oauth.consumerKey=  
oauth.consumerSecret=  
oauth.accessToken=  
oauth.accessTokenSecret=
```

Now that we’ve set the authentication to our Twitter application we can finally go into the code!

### Step 4 — The Code

Create a new Kotlin file in your **src/main/kotlin** directory with the name **TwitterBot**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485728903/USq7hjFK9.png)

And paste inside the following code overriding whatever is currently there:

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
     * Reads all tweets from `tweets.txt` file under resources
     * directory and tweet them one by one.
     * with a delay between them
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
     *        to read from
     * @return list of strings representing each line in the file.
     */
    private fun getResourceAsLines(resource: String): List<String> =
        TwitterBot.javaClass.classLoader
            .getResource(resource)
            .readText()
            .lines()

    /**
     * Publishing a given tweet into the Twitter account.
     */
    private fun sendTweet(tweet: String) = kotlin.runCatching {
        TwitterFactory.getSingleton().updateStatus(tweet)
    }
        .onSuccess { status: Status -> println(status) }
        .onFailure { it.printStackTrace() }

    /**
     * Waits for a constant amount of time between each every 2
     * tweets we want to publish.
     *
     * @param delayInSeconds the number of minutes to delay,
     *  30min by default.
     */
    private fun waitUntilNextTweet(delayInSeconds: Long = 30) =
        kotlin.runCatching {
            println("Sleeping for $delayInSeconds minutes...")
            // Change here to increase or decrease the delay
            Thread.sleep(delayInSeconds * 60000)
        }
            .onFailure { it.printStackTrace() }
}

```

We won’t go deeply into the code above, however, it’s relatively simple and documented to try and make it as easy as possible for the reader to understand.

Again, as mentioned in the original article, you’ll also want to go ahead and read the [Twitter API Limits](https://help.twitter.com/en/rules-and-policies/twitter-limits) to familiarize yourself with how often you can tweet without getting bulldozed by your new sentient AI overlords.

### Step 5— Running our application!

You can run the program from the command line in the directory of the project by running the following command:

```shell
./gradlew clean build && java -jar ./build/libs/twitter-bot-1.0-SNAPSHOT.jar
```

If it worked, you’ll see something like the following in your debug terminal at the bottom of the screen:

![bot output](https://cdn.hashnode.com/res/hashnode/image/upload/v1639485730383/_IXLpBWjd.png)

Congratulations! we have a working bot!

The finished bot is available in the repository from step 2 under the branch name `complete_bot` ([https://github.com/yonatankarp/twitter-bot/tree/complete\_bot](https://github.com/yonatankarp/twitter-bot/tree/complete_bot))