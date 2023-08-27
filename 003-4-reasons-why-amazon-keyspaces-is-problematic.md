---
title: "4 Reasons Why Amazon Keyspaces is Problematic!"
subtitle: "How we workaround keyspaces missing features"
slug: 4-reasons-why-amazon-keyspaces-is-problematic
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: cassandra, cloud, aws, databases
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/3cFjeikAqCY/upload/v1640871546882/d9stnU3Iv.jpeg
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: false
seoTitle: "4 Reasons Why Amazon Keyspaces Falls Short: Missing Features and Worka"
seoDescription: "Explore the limitations of Amazon Keyspaces, a Cassandra-compatible database. Is it the right choice for your system?"
seriesSlug: 
# Table of contents
enableToc: false
saveAsDraft: false
---

Around a year ago, my team and I were tasked with building a high-performance system capable of handling a large volume of requests. After careful consideration, we chose Cassandra as our database.

When it came to productizing our system, we explored three main options:

* [**Self-hosted Cassandra**](https://cassandra.apache.org/_/index.html): We ruled this out due to the need for hardware maintenance.
    
* [**DataStax**](https://www.datastax.com/): Dubbed the "Confluence-Kafka" of Cassandra, it offers a fully cloud-based implementation managed by the DataStax team.
    
* [**Amazon Keyspaces**](https://aws.amazon.com/keyspaces/): Amazon's version of Cassandra's CQL language.
    

Given our heavy use of AWS, we opted for Amazon Keyspaces, taking into account the existing AWS support and competitive pricing.

However, little did we know that Amazon Keyspaces only supports about 40% of the features offered by Cassandra at the time of writing this article. You can refer to [this](https://docs.aws.amazon.com/keyspaces/latest/devguide/cassandra-apis.html) link for an up-to-date overview of the supported features.

Let's discuss the key missing features we encountered and the workarounds we implemented to address them.

## 1\. UDT's (User Defined Types)

Cassandra allows users to define custom data types, enabling the creation of columns in the database that precisely match the required data. This feature ensures that the database models accurately reflect the service level models.

We discovered two alternatives to address this issue:

* Model-specific column naming convention: For instance, assuming we have a `Person` model with `id` and `name` properties, we create two columns in the database: `person_id` and `person_name`.
    
* Using tuples to store data in a single column: Alternatively, we can create a single column `person` with the type `tuple<int, string>`.
    

Both solutions have drawbacks. The first option allows easy understanding of each field's type but makes it harder to visualize the overall model since it spans multiple columns. The second option provides a holistic view of the model but makes it challenging to understand each field in isolation. Consequently, we employed both approaches based on the specific use case. For example, when storing a price, consisting of an amount and a currency, it makes sense to use a tuple since they are inherently linked and lack individual meaning. However, for the `person` example, it is easier to comprehend each field independently.

## 2\. TTL (Time To Live)

Cassandra supports TTL, allowing rows to be automatically removed from the database after a predefined time. We planned to leverage this feature to store and maintain our idempotency keys for requests. However, the absence of this feature resulted in numerous useless rows that persisted in the database for at least 12 hours.

To address this issue, we decided to store our keys outside the database in a [Redis](https://redis.io/) cluster. Another option would be to implement a scheduled job to clean the database, but this would introduce unnecessary additional load.

On a positive note, our AWS account manager informed us that this feature is expected to be available in early 2023! 🎉

## 3\. Syntax

While all features in Amazon Keyspaces are compatible with Cassandra, the reverse is not true. During development, we discovered that certain features like `in` and `not equals` were not yet supported by the database.

Unfortunately, we often couldn't find suitable replacements for these features, resulting in suboptimal queries. Additionally, we learned that all data structures in Keyspaces must be immutable (`frozen`), unlike Cassandra. For example, in the case of our `person` model, the `tuple` representation would need to be defined as follows in Keyspaces:

```kotlin
frozen<tuple<int, string>>
```

To avoid disrupting our clusters, we created a separate Keyspaces instance for Continuous Integration (CI) purposes. This allowed us to run scripts and validate their functionality, ensuring they worked as expected (or worked at all in some cases).

## 4\. Materialized Views

As per Cassandra's documentation:

> Each such view is a set of rows that corresponds to rows that are present in the underlying, or base, table specified in the SELECT statement. A materialized view cannot be directly updated, but updates to the base table will cause corresponding updates in the view.

The absence of this feature proved to be a significant hurdle throughout our development process. To overcome it, we ended up creating an actual table to store the data and update the entries. However, as each row in Cassandra is internally immutable, updating rows becomes a more resource-intensive operation, negatively impacting performance.

---

I hope you enjoyed this journey and learned something new. If you want to stay updated with my latest thoughts and ideas, feel free to register for my [**newsletter**](https://yonatankarp.com/newsletter). You can also find me on [**LinkedIn**](https://www.linkedin.com/in/yonatankarp/) or [**Twitter**](https://twitter.com/yonatan_karp). Let's stay connected and keep the conversation going!
