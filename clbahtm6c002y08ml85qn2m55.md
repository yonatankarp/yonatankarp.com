# How to break your HashMap in less than a minute

> **TL;DR:** Never use mutable objects inside a HashMap!

After I wrote my article [**How does HashMap work in Java?**](https://medium.com/@yonatankarp/how-does-hashmap-work-in-java-368a239339d2) a few people suggested more topics `HashMap`. Thus, I decided to make a short series of articles out of it.

In today’s article, we will talk about JVM HashMaps. We will show how you can break them if you’re not careful. But keep in mind, while we're talking about the JVM world, the same applies to most modern languages.

In this article, I will write the implementation using Java. I will then explain the root cause of the problem, and how to solve the issues I created.

# The How

Consider the following simple class. It wraps an integer value inside an object and allows the developer to get or set the value of the class:

```java
public class IntWrapper {
    private int value;

    public IntWrapper(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object other) {
        if (this == other)return true;
        if (!(other instanceof IntWrapper))return false;
        return value == ((IntWrapper) other).value;
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

Now let’s use our class and add it to a `HashMap`:

```java
Map<IntWrapper, String> map = new HashMap<>();
IntWrapper myInteger = new IntWrapper(1);
map.put(myInteger, "");
myInteger.setValue(2);

if(map.containsKey(myInteger)) {
    System.out.println("Our int was found!");
} else {
    System.out.println("Sorry, nobody is home :(");
}
```

What would be printed here? if your answer was `Sorry, nobody is home :(` you’re correct. But why?

# The Why

The problem is that we are using a mutable class in HashMap, and mutating it after. As I mentioned in my previous article, adding a new key/value to a HashMap will calculate the key’s hash. it will then store them in a pair inside the relevant bucket:

![](https://cdn-images-1.medium.com/max/1600/1*UVvdHtSHcnMofPVdogGKtg.png align="center")

But in our case, we changed the value of the class. So when we calculate the hash code of the updated object, we will (most probably) end up in a different bucket. Hence, when the HashMap checks the bucket, it indeed does not include the required object!

![](https://cdn-images-1.medium.com/max/1600/1*-ehInn6beG7s3ry6JnqOBQ.png align="center")

# **Can we avoid this problem?**

![](https://cdn-images-1.medium.com/max/1600/0*a_vh_4bR2vHyx220 align="left")

Yes, we can! to avoid the problem we should use an immutable class instead of a mutable one. That means that once the class creates its state cannot change.

Let’s start with a simple plain old java implementation for the class:

```java
public class IntWrapper {
    private final int value;

    public IntWrapper(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    @Override
    public boolean equals(Object other) {
        if (this == other) return true;
        if (!(other instanceof IntWrapper)) return false;
        return value == ((IntWrapper) other).value;
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

As you can see in this implementation, the class value can be set only via the constructor. This guarantees that it will not change later on. By marking the field as final we ensure that it cannot change on runtime, even using [reflection](https://www.oracle.com/technical-resources/articles/java/javareflection.html). And thus our class is truly immutable.

# Can we do even better?

The code works now. But is still very verbose, and as of Java 14, we have a new feature (that became official in Java 19) — Records!

What are records?

> JDK 14 introduces records, which are a new kind of type declaration. Like an `enum`, a record is a restricted form of a class. It’s ideal for "plain data carriers," classes that contain data not meant to be altered and only the most fundamental methods such as constructors and accessors.

What does that mean? By defining a class as a record, we will get out-of-the-box the following methods implemented for us:

*   Constructor that will assign all inputs to the class members
    
*   Getters for all class members & a private final field associated with them
    
*   `toString()` implementation
    
*   `hashCode()` & `equals()`
    

If you want to read more about records, and their usage, check [Oracle’s official documentation](https://docs.oracle.com/en/java/javase/14/language/records.html)

So let’s try it:

```java
public record IntWrapper(int value) {
}
```

As you can see, our code is much simpler now, and if we will try to repeat our map change from before we will get an error:

```java
Map<IntWrapper, String> map = new HashMap<>();
IntWrapper myInteger = new IntWrapper(1);
map.put(myInteger, "I am a nice int value!");
myInteger.setValue(2); // Compilation error!

if(map.containsKey(myInteger)) {
    System.out.println("Our int was found!");
} else {
    System.out.println("Sorry, nobody home :(");
}
```

If you use an older version of Java, you can still achieve this functionality with [Project Lombok.](https://projectlombok.org/) By using annotation `@Value`. More details could is available [here](https://projectlombok.org/features/Value).

# Conclusion

![](https://cdn-images-1.medium.com/max/1600/0*J4M6sTFLBqyDRLw8 align="left")

Whenever you’re using a HashMap (in Java or any other language), make sure you’re using immutable objects. Otherwise, it might lead to unexpected behaviors in your code during its execution.

# Credits

*   Photo by [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=Hashnode&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=Hashnode&utm_medium=referral)
    
*   Photo by [Elisa Ventur](https://unsplash.com/@elisa_ventur?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)
    
*   Photo by [Tetiana SHYSHKINA](https://unsplash.com/@shyshkina?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)