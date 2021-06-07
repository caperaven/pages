## OOP vs ECS

## Terms

1. [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming) - object orientated programming
1. [FP](https://en.wikipedia.org/wiki/Functional_programming)  - functional programming
1. [ECS](https://en.wikipedia.org/wiki/Entity_component_system) - entity component system
1. [DOD](https://en.wikipedia.org/wiki/Data-oriented_design) - data orientated design

## Introduction
In this document we will be looking at the question "Should I use OOP or ECS?".  
To get started you should be somewhat familiar with OOP and ECS theory.  
ECS is part of DOD that competes with OOP on how large performant applications should be build.

In the ECS wikipedia document (lined above) it states that ECS is used in the gaming industry.  
This is true, but the question we want to look at is why and what benefits are there for my normal javascript projects.
In particular, we will be looking at enterprise scale application where we are dealing with large data.

There are people in the camp that are OOP purists and on the other side functional programming purists.
To some degree the ECS crowed falls in the functional purist group.
I don't care about being a purist, I would rather look at the use cases and what solves what problem best.

## The horrid OOP issue
There is one critical flaw with OOP, its memory footprint.  
Base classes can have properties or functions that you don't want in your class.  
Static values are duplicated across all instances.  
It is very ridget in its structure.  

DOD solves this problem by separating the data from the functionality.  
ECS makes use of this by allowing dynamic data structures and systems that act on that data.
This means you can have one copy of the function in memory not per instance, actually all redundant memory use is elevated.

When working with large amount of data objects ECS / DOD is the clear winner.

## Questions
Q: Can DOD help me on small scale applications?  
A: The short answer is yes, FP has never gone away, it just requires a different mindset.

Q: Can I use OOP in my small scale application?  
A: Sure, there are still very good use cases for OOP.

Q: How can I determine if I should use OOP or DOD (ECS)?
A: You need to understand the strength and weakness of both and design your code according to your needs.

## OOP at its best
In scenarios where I have a lot of objects of a given type, ECS just is the better pattern to use. 
1. You only have the data representing each object.
1. You can streamline the data even more with only data you need for that instance.
1. You only have one instance of a function in memory that acts on the data.

There are design patterns though where OOP is the clear winner for me.

1. Manager
1. Provider
1. Controller
1. Factories

These represent single instance complex objects that manage memory usage and can have an extended lifespan often spanning the lifetime of the application.
Granted they don't need to be single instances, but you are not working with hundreds of instances either.

If you answer yes to any of these questions, that feature might prefer OOP.

1. Does this object manage memory and is responsible for cleanup on disposal.
1. Does inheritance simplify defining complex behaviour and types.
1. Does the api you use require predefined objects. Web components being an example of this.

Also keep in mind that you can use a mix bag of things.  
1. Static properties can be made constants outside the class.
1. Ask this question: "must this function exist on each instance?", if no, consider making it extern to the class.
1. Is this a all and nothing deal. In other words, when this instance comes into existance everything that makes it tick should at that time also come into existance. 
On disposing that object, everything that came into existence with the object should also be disposed of.
