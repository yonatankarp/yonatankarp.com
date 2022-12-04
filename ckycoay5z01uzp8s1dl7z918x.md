# 4 reasons why Amazon-Keyspaces is the worst!

About a year ago, my team and I received the task of building a resilient, robust, consistent, and fast system that will handle a big volume of requests in our system. After many considerations, we decided to use Cassandra as our database.

When we started to think about the productization of our system, we had three main options:

*   [**Self-hosted Cassandra**](https://cassandra.apache.org/_/index.html) - We decided against it, as we didn't want to maintain our own hardware.
    
*   [**DataStax**](https://www.datastax.com/) - The "Confluence-Kafka" of Cassandra. A fully cloud-based implementation of Cassandra managed by the DataStax team.
    
*   [**Amazon Keyspaces**](https://aws.amazon.com/keyspaces/) - Amazon's compatible implementation of Cassandra's CQL language.
    

Since our company uses AWS heavily, we decided to go with the last, as we already have the AWS support, we will get the best price, etc.

Little did we know, Amazon Keyspaces supports about 40% of the features of Cassandra at the time I'm writing this article. If you want to the latest state of the supported features you can visit [this](https://docs.aws.amazon.com/keyspaces/latest/devguide/cassandra-apis.html) link.

I would list the main features that my team and I have found the most relevant missing features, and the workarounds we did to resolve them (if possible)

# UDT's (User Defined Types)

Cassandra allows users to define their own data types. That is a great feature that allows you to create columns in the database with this specific type and make sure that your database contains only the required data. More importantly, it allows you to have models at the database level that are an exact reflection of your service level models.

There are two alternatives for this issue:

*   column name prefix of the model: Let's assume that we have the model `Person` with the properties `id` and `name`. We will create two columns in the database: `person_id` and `person_name`.
    
*   Using tuples to store the data in a single column: Alternatively, we can create a single column `person` of the type `tuple<int, string>`
    

As you can imagine, both solutions aren't perfect. While in the first case, you can easily understand the type of each field. It's harder to see the "big picture" of the model as it's split over multiple columns (and vise versa). In the end, we used both options based on the case. For example, imagine a case where you need to store a price in your database. A price is a group of an amount and a currency. In such a case it makes sense to store both of them as a tuple since they need to live together and have no meaning by themselves. On the other hand, if we think about our `person` example - it's much easier to understand each field independently from each other.

# TTL (Time To Live)

Cassandra supports TTL for entry in the database. That means that the row will disappear from the database once it reaches the pre-defined TTL. In our case, we wanted to use this feature to store and maintain our requests idempotency keys. However, the lacking of this feature made us end up with endless rows that are useless often the first 12h they've been added in the database.

To store this issue, what we decided to do, is to store our keys outside of the database in a [Redis](https://redis.io/) cluster. An alternative option is to have a scheduled job that will clean the database, however, this will cause extra load on our database that is not needed.

Some good news, according to the AWS account manager, this feature should be available at the beginning of 2023! 🎉

# Syntax

While all of the features in Amazon Keyspaces are compatible with Cassandra, it another way around isn't true. While developing, we discovered that features such as `in`, `not equals`, and many more and just not yet supported by the database.

Unfortunately, in many cases, we didn't find a proper replacement for this issue, and many times we've written queries that are sub-optimal just because of this restriction.

Moreover, we discovered that any data structure in Keyspaces has to be immutable (`frozen`) otherwise it cannot be created (unlike Cassandra). If we'll take our. example of the. `person` model from before, the `tuple` representation of it would have to be as follow:

```kotlin
frozen<tuple<int, string>>
```

Eventually, to avoid breaking our clusters, we created Keyspaces for CI that its only purpose was to run our scripts and validate that they're working as expected (or at all in some cases).

# Materialized Views

From Cassandra's documentation:

> Each such view is a set of rows that corresponds to rows that are present in the underlying, or base, table specified in the SELECT statement. A materialized view cannot be directly updated, but updates to the base table will cause corresponding updates in the view.

This missing feature beat us again and again over our development process. In the end, the way we resolved it was to create an actual table that will store the data, and update the entries. However, as in Cassandra each row is immutable (internally) updating the rows is a much more expensive operation in terms of performance.