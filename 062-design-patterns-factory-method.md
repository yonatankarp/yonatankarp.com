---
title: "Design Patterns - Factory Method"
subtitle: ""
slug: design-patterns-factory-method
# - You can find tags information from here https://github.com/Hashnode/support/blob/main/misc/tags.json
tags: programming-blogs, kotlin, design-patterns, kotlin-beginner, clean-code
domain: https://yonatankarp.com
# Note: You must upload the image to Hashnode's CDN, before you can use it here.
# - To upload, Login to Hashnode and go to https://hashnode.com/uploader
#   Use the URL that is generated after the upload.
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1696450075539/NRr3n_Rs8.webp?auto=format
# Should the post be ignored? When true it will not be picked up by Hashnode.
# - It's useful when you want to keep the post in your repo but don't
#   want it to be picked up by Hashnode.
ignorePost: true
seoTitle: "Understanding the Factory Method: Key Concepts and Examples"
seoDescription: "Master the Factory Method design pattern for efficient object creation in software development with real-world examples and easy-to-follow guide."
seriesSlug: jvm
# Table of contents
enableToc: true
saveAsDraft: false
---

> **TL;DR**: Factory Method is a design pattern that defines an interface for creating objects, allowing subclasses to decide which class to instantiate, thus delegating instantiation logic to child classes.

## Also known as

* Virtual Constructor


## Intent

Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

## Explanation

Real-world example

> Blacksmith manufactures weapons. Elves require Elvish weapons and orcs require Orcish weapons. Depending on the customer at hand the right type of blacksmith is summoned.

In plain words

> It provides a way to delegate the instantiation logic to child classes.

Wikipedia says

> In class-based programming, the factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method — either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.

**Programmatic Example**

Take our blacksmith example above. First of all, we have a `Blacksmith` interface and some implementations for it:

```kotlin
internal interface Blacksmith {
  fun manufactureWeapon(weaponType: WeaponType): Weapon
}

internal class ElfBlacksmith : Blacksmith {
  override fun manufactureWeapon(weaponType: WeaponType): Weapon =
    ELF_ARSENAL.getOrElse(weaponType) {
      throw IllegalArgumentException("Weapon type $weaponType is not supported by elf blacksmith.")
    }
}

internal class OrcBlacksmith : Blacksmith {
  override fun manufactureWeapon(weaponType: WeaponType): Weapon =
    ORC_ARSENAL.getOrElse(weaponType) {
      throw IllegalArgumentException("Weapon type $weaponType is not supported by the orc blacksmith.")
    }
}
```

When the customers come, the correct type of blacksmith is summoned and the requested weapons are manufactured:

```kotlin
// Orc
var blacksmith: Blacksmith = OrcBlacksmith()

var weapon: Weapon = blacksmith.manufactureWeapon(WeaponType.SPEAR)
logger.info("$blacksmith manufactured ${weapon.weaponType.title}")

weapon = blacksmith.manufactureWeapon(WeaponType.AXE)
logger.info("$blacksmith manufactured ${weapon.weaponType.title}")

// Elf
blacksmith = ElfBlacksmith()

weapon = blacksmith.manufactureWeapon(WeaponType.SPEAR)
logger.info("$blacksmith manufactured ${weapon.weaponType.title}")

weapon = blacksmith.manufactureWeapon(WeaponType.AXE)
logger.info("$blacksmith manufactured ${weapon.weaponType.title}")
```

Program output:

```plaintext
The orc blacksmith manufactured spear
The orc blacksmith manufactured axe
The elf blacksmith manufactured spear
The elf blacksmith manufactured axe
```

## Class diagram

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1695931268294/bb81f1d5-ec79-4150-8182-93c9d83ca6e9.png align="center")

## Applicability

Use the Factory Method pattern when:

* The class cannot anticipate the class of objects it must create.

* The class wants its subclasses to specify the objects it creates.

* Classes delegate responsibility to one of several helper subclasses, and you want to localize the knowledge of which helper subclass is the delegate.


## **Code Examples**

All code examples and tests can be found in the [**Kotlin Design Patterns repository**](https://github.com/yonatankarp/kotlin-design-patterns/tree/main/factory-method)

## Credits

* [Design Patterns: Elements of Reusable Object-Oriented Software](https://www.amazon.com/gp/product/0201633612/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0201633612&linkCode=as2&tag=javadesignpat-20&linkId=675d49790ce11db99d90bde47f1aeb59)

* [Head First Design Patterns: A Brain-Friendly Guide](https://www.amazon.com/gp/product/0596007124/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0596007124&linkCode=as2&tag=javadesignpat-20&linkId=6b8b6eea86021af6c8e3cd3fc382cb5b)

* [Refactoring to Patterns](https://www.amazon.com/gp/product/0321213351/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0321213351&linkCode=as2&tag=javadesignpat-20&linkId=2a76fcb387234bc71b1c61150b3cc3a7)
