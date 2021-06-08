# Development Guide

## Introduction
The purpose of this document is to lay down some development considerations.

## Maintainability
A large focus should be on maintainability of the code.  
There are a number of things you can do to make your code more readable, understandable and maintainable.

1. Use short but descriptive names
1. Comment your code. Comments on classes and function.
1. Separation of concern pattern is important for this.

### Comments
You should be using JSDoc to comment your code.

```js
/**
 * This is my class that does my stuff
 */
class MyClass {
    /**
     * This method does ....
     * @param param1 {string} description of parameter
     * @param param2 {number} description of parameter
     * @return {string} result description
     */
    myStuff(param1, param2 = "ABC") {
        // 1. Description of step 1 
        ... step 1 code
        
        // 2. Description of step 2 
        ... step 2 code
        
        // 3. Description of step 3 
        ... step 3 code

        // 4. return result
        return result;
    }
}
```

The comments in the function is only there to:

1. Help you think through the process steps you need to take to perform a given action.
1. Document the process steps and create functional blocks in your code for maintenance purposes.

Note that parameter comment just contain:
1
1. Parameter name
1. Type
1. Description

If your function returns a value that must also be in the documentation including type and description.

### Capitalize global constants
Constants not in scope of a class, object or function should be capitalized.
Separate different words using snake case.

```js
const PADDING       = 10;
const LEFT_PADDING  = 5;
```

Constants inside functions should still follow the standard naming convention.  
In mose cases that would be camel case.

I have often noticed that people use properties when constants would be better.  
Consider this memory usage scenario.  
I have a static value in an object defined as a property.  
I have 100,000 instances of this object in memory.  
This means I have 100,000 copies of that property in memory.  
Since this is a static value, I could have made it a constant only ever having 1 instance in memory.
The gains in memory usage between 1 vs 100,000 should be obvious.

Q: When do I use constants instead of properties?
A: If you ask the following questions, and the answer is "yes" it should be a property.

1. Does this value define something unique about this type that is not universal.
1. Does this value assist in operations in the object
1. Does this value change

## Use enums when working with static options
Javascript does not have native enums, but you can use frozen object literals that act like enums.

```js
export const MyEnum = Object.freeze({
    OPTION1: "option1",
    OPTION2: "option2",
    OPTION3: "option3",
    OPTION4: "option4"
})
```

```js
someObject.someFunction(value, MyEnum.OPTION1);
```

When using the enum as part of a convention over code pattern, you can use convention based strings as the value.
If however you are just using it as a reference type rather define the values as hex values as they use less memory.

```js
export const MyEnum = Object.freeze({
    OPTION1: 0x1,
    OPTION2: 0x2,
    OPTION3: 0x3,
    OPTION4: 0x4
})
```

When intending to use enum as an options set, use bitwise structure for the values.

```js
export const MyEnum = Object.freeze({
    OPTION1: 1,
    OPTION2: 2,
    OPTION3: 4,
    OPTION4: 8
})

// Create options
const options = MyEnum.OPTION1 | MyEnum.OPTION3;
// Check options
const isValid1 = !!(options & MyEnum.OPTION1)  // is true
const isValid2 = !!(options & MyEnum.OPTION2)  // is false
```

Granted one enum is not going to make much of a difference, but the cumulative effect of many enums over an enterprise application will.

## Classes vs Functions vs Object Literals
Not everything should be classes and not everything should be functions.  
When do I use what and what are the rules.  

#### Q: When do I use classes  
A: There are a couple scenarios  

1. Do you want to manage multiple copies of a type? If the answer is yes, use a class.
1. I want to derive from a type where base functionality is defined on a base type you inherit from.
1. Use classes to emulate namespaces to static functions.

#### Q: When and how to use functions  
A: Note that this does not apply to methods (functions on classes).  
Functions perform a function or action.   
Functions can return a value but does not always have to. It depends on what kinds of function is being performed.  
Everything the function needs must be passed to the function using parameters.  
Functions should not access constants or variables outside its own scope but can call other functions.
Methods can have access to members of it's class, but not outside the scope of it's class.

Avoid [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) as all cost.   
Closures have serious performance detriments. 

Functions should not have more than 3 parameters.  
Parameters as far as possible should have [default values](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters).  
When you have a complex function or method that can take in a lot of parameters of variations in what parameters may be set, rather use a object literal.

```js
function complex(options) {
    const v1 = options.v1 || "value1";
    const v2 = options.v2 || "value2";
    ...
    
    // do stuff with values
}
```

#### Q: When to use object literals
A: Object literals are very good as data carriers but should not be confused as cheap classes.
The moment you start adding functions to an object literal you should consider making it a class.
This is not a rule, you need to think this through.

Use object literals when storing or transporting data in your application.
A big difference between classes and object literals are that classes are instance driven for types, objects are data structures.
For example, I need three instances of a car type including its data and operational functionality.
This is a clear case for a class as it contains data and functionality combined in one type.
The object literal is more focused on data.  
You should not be adding operational features to an object literal.  
If you need actions to be taken on the data, pass the object literal to a function or class that will deal with that.
In entity, component, system patterns, the system will execute functions based on the data.

You can't pass classes to web workers, this is where object literals come in.  
A proper design using the entity, component system pattern can alleviate some of these cross thread issues.

Object literals can have calculated field type operations.  
An example of this could be defining a full name value based on the first name and last name property on the object literal.

```js
const person = {
    firstName: "John",
    lastName: "Doe",

    get fullName() {
        return `${this.firstName} ${this.lastName}`
    }
}

console.log(person.fullName);
```

This is not trying to perform some function, this complex object still acts like a data structure.

Note you can't send object literals with functions on to a web worker.  
For the most part, passing objects between threads should only contain raw data. No functions, no properties.

### Summary on classes vs functions vs object literals

Use classes when you want multiple instances of complex functional objects based on a type.  
Functions are self-contained features, operating on what was passed on and may return a result.
Object literals are data structures used for storing and passing data around.

## Make use of modern syntax features

### Arrow functions (() => {})
[arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### Array spread syntax (...array)
[spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

### Operational chaining (?.)
[optional chaining](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)

### Nullish coalescing operator (??)
[nullish coalescing operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)

### Asynchronous programming
[async await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)
[using promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)