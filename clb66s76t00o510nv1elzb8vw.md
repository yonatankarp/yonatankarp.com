# How does HashMap work in Java?

As an interviewer, I like to ask this question. I Like it as it shows how much the candidate understands data structures. If the candidate understands the JVM internals. And generally the way of thinking when thrown into an unknown area.

So why am I sharing this information with you? I have seen many candidates that couldn’t answer this correctly (juniors, mid-levels, and seniors). Since I’ve been asking about it for a long time, I believe I should replace it and share the answer with the world.

In this article, I would walk you through the HashMap question. I will include a detailed explanation for each of the steps.

Let’s start!

### What is the contract in Java between the equals() and hashCode() functions?

![](https://cdn-images-1.medium.com/max/1600/1*o-EXRh62Vv1wF6hGc8enkg.jpeg align="left")

As we all know, in Java all objects inherit either directly or indirectly from the Object class. That means that each object includes the `equals()` and `hashCode()` functions.

If you would read the documentation of `equals()` you could notice the following:

> Note that it is generally necessary to override the `hashCode` method whenever this method is overridden, so as to maintain the general contract for the `hashCode` method, which states that equal objects must have equal hash codes.

But what does that mean?

It means that if we have two objects considered equal, they must have the same hash code. However, if the objects are not equal, there is no guarantee that they would have different hash codes.

### What is the complexity of getting an element to a HashMap?

![](https://cdn-images-1.medium.com/max/1600/1*OigNoAIlGNKT7ZfyQ5L7UQ.jpeg align="left")

Any 1st-year student of computer science will tell you, getting an element from a HashMap has a constant time of `O(1)`.

That was easy! or is that so?

### What would happen if hashCode() is overridden to return a constant value?

Let’s assume that as a developer I have created the following class:

```java
class MyClass {
    @Override
    public boolean equals(Object other) {
        if(!(other instanceof MyClass)) return false;
        return this == other;
    }

    @Override
    public in hashCode() {
        return 1;
    }
}
```

And now in my code, I am creating a million objects and storing them in a hash map:

```java
Map<MyClass, Integer> map = new HashMap<>();
IntStream.range(0, 1_000_000)
         .forEach(i -> map.put(new MyClass(), i));
```

How would the new hash code function impact our performance if at all?

To answer this question we must first dig into how exactly HashMap is working in Java.

When we add a new element to your map, the hash code of this key is calculated first. According to its value, a bucket is selected. That bucket will contain the value (in this case our object). In case of collision (2 or more objects with the same hash code), the key and the value stored as a pair using some sort of data structure (e.g. a linked list)

![](https://cdn-images-1.medium.com/max/1600/1*U5d_HNmHikJF_AkUA6n55Q.png align="center")

Now let’s assume that we want to get an element from the map the following actions will happen:

*   the hash code of the key is calculated using the `hashCode()` function.
    
*   the HashMap iterates over the linked list using the `equals()` function. It will finish when it will find the correct pair of key & value
    
*   Once the correct key is found the value is returned or null if no key was found.
    

Note that in general, the assumption is that the hash function is evenly distributed. That means that we expect a very small amount of elements in each bucket. Thus the search for the correct key can be considered as a constant time.

Yet, in our example, all the keys are going to have the same hash code, and thus end up in the same bucket. Hence, getting a value from the map will need (in the worst case) scanning the entire linked list. That would take a linear time of `O(n)`.

![](https://cdn-images-1.medium.com/max/1600/1*8s8sB71hNyVPARiINfjO9A.png align="center")

### Can we do better than linear time?

![](https://cdn-images-1.medium.com/max/1600/1*-SHIfdSfBVHgBNHUpIjNnQ.jpeg align="left")

Our problem now is that the bucket uses a linked list, a data structure that is unsorted. What if we would use a data structure that is sorted by nature?

We can replace our bucket implementation with a balanced binary tree. By doing so, we can ensure that each element in the bucket could be found with the worst-case complexity of `O(log(n))`. This is much better than what we had before.

![](https://cdn-images-1.medium.com/max/1600/1*84NP4Y4RVJgPtYtOfJS93g.png align="center")

In fact, since java 8 the internal implementation of HashMap was changed exactly like that.

### Bonus: How can you implement HashSet using HashMap?

![](https://cdn-images-1.medium.com/max/1600/1*QCIL57ECXC7CSnWzuNqeTg.jpeg align="left")

Let’s start with the question, what is a HashSet?

A HashSet is a set (an unordered collection of elements), where the same hash code only exists once.

![](https://cdn-images-1.medium.com/max/1600/1*WxT39yTmAiuDFp4HHeUYVw.png align="center")

We can easily implement a `HashSet` using `HashMap`. We will do so by using our value for the set as the key for the map, and null as the value. In fact, that’s again exactly the implementation Java chose.

### Conclusion

As you can see, the question covers many areas:

*   General understanding of data structures
    
*   Understanding of Java internals
    
*   Check how the candidate thinks in case they do not remember the internals of Java (to be clear, nobody expects the Wikipedia answer to this question)
    

Moreover, the question could be extended to cover more topics such as concurrency for example (how would you implement distributed HashMap?).

### More Info

*   [Java Object API](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html)
    
*   [Wikipedia — Hash Table](https://en.wikipedia.org/wiki/Hash_table)
    
*   [Java HashMap under the hood](https://www.baeldung.com/java-hashmap-advanced)
    
*   [Java HashMap — HashMap in Java](https://www.digitalocean.com/community/tutorials/java-hashmap)