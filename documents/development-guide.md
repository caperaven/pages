# Development Guide

## Introduction
The purpose of this document is to lay down some development considerations.

## Maintainability
A large focus should be on maintainability of the code.  
There are a number of things you can do to make your code more readable, understandable and maintainable.

1. Use short but descriptive names
1. Comment your code. Comments on classes and function.
1. Separation of concern pattern is important for this.

### Global constants
Global constants should be all uppercase.  
New words should be separated with an underscore.
```js
const GLOBAL_CONST: 10
```

### Private members of a class
Private members are prefixed with an underscore.    
Use camel case for the member name.  

```js
class Test {
    _privateFunction() {
    }
}
```

## Camel case
[Camel case](https://en.wikipedia.org/wiki/Camel_case) is the default case that most javascript developers use.  
It is recommended that you use camel case as the default case.

## Snake case
Some people like using [snake case](https://en.wikipedia.org/wiki/Snake_case) as part of their normal coding standard.  
I think there is a place for snake case but, more part of a convention over code example.
When working with attributes, casing is not allowed.  
In those cases all letters are lowercase and words are separated with "-".    
Object member names don't support "-" in the name.   
In those cases you can lean a bit on snake case to help you out.  
Thus, if you are using a convention where an attribute value ties to a member name, use snake case.

## Comments
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

## Structs

Javascript does not have structs as such.  
You do have object literals and, you can use them in the same way.

```js
export const device = Object.freeze({
    MOBILE: "mobile",
    DESKTOP: "desktop",
    get(width, maxMobileWidth) {
        return width <= maxMobileWidth ? device.MOBILE : device.DESKTOP
    }
})
```

In this context, methods must operate in context of the struct and perform either a get or set operation.  
This means that functions can not operate outside the scope of the struct.

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

## Class structure
Consider the following class example.

```js
class MyClass {
    // Constructor
    constructor() {
        // used in event handlers
        this._doSomethingHandler = this._doSomething.bind(this);        
    }
    
    // Destructor
    dispose() {
        this._doSomethingHandler = null;
        return null;
    }
    
    _doSomething() {
        // do stuff
    }
}
```

1. functions such as _doSomethingHandler shares the same name as the source function with the word "Handler" attached.
1. dispose is convention function you need to call to dispose of your content.
1. dispose function can return null if you require null chaining.

```js
let myObj = new MyClass();
// do stuff
myObj = myObj.dispose();
```

Setting the variable to null makes it ready for the [GC](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management) to pick up.  
Nullifying resources you instanced during the life of the object in the dispose function is a good way to ensure you don't have any memory leaks.  
This convention borrows from the [dispose pattern](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose).

## Make use of modern syntax features

### Arrow functions (() => {})
[arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### Array spread syntax (...array)
[spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

This is really useful for many array or parameter operations.
It is important that each developer has a good grasp on spread operators to make more complex and verbose tasks simple.
Here is an example.  
I want to add a number of values from one array to another array at a given position.

<strong>old way</strong>
```js
const index = 2;
const newArray = [4,5,6];
const existingArray = [1,2,3];

// DO NOT DO
for(let i = 0;i < newArray.length;i ++) {
    existingArray.splice(index +i +1, 0, item);
}
```

<strong>new way</strong>
```js
existingArray.splice(index + 1, ...newArray);
```

### Operational chaining (?.)
[optional chaining](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining) will cut back a lot of code and even help prevent some bugs.  
It's a simple syntax that makes your code a lot more readable and help you express you intent instead of error prevention.

<strong>old way</strong>
```js
let value;
if (obj !== undefined && obj.property != undefined) {
    value = obj.property.value;
}
else {
    value = "a";
}
```

<strong>new way</strong>
```js
const value = obj?.property?.value || "a";
```

The new way reads much better but performs all the same logic as the old way.
In the new way the [or](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR) operator performs the same task as the else. 

### Nullish coalescing operator (??)
[nullish coalescing operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)

### Asynchronous programming
[async await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)
[using promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

Q: When do I use async.  
A: This is not a simple answer and requires the developer to think about the thread usage.    
There are some cases where you can just make things async as a rule.

1. All event functions should be async
1. Component lifecycle functions (connectedCallback, disconnectedCallback) should be async.
1. All factory functions should be async

Since async helps alleviate thread blocking some libraries feel that every function must be async.    
Puppeteer being an example of that.  

Async code does use more memory and also cpu time to resolve the promises.  
The balance you need to work out in your project is around non-blocking behaviour vs performance.
Input events are expensive and often create blocking large tasks.
Making them async helps keep them small ensuring a better framerate.

Async does complicate matters.  
Debugging can also become more difficult.

Long story short, you need to consider the tradeoff between processing and blocking behaviour.

### Web workers
This is part of the async though pattern.    
All javascript runs off a single thread we call the UI thread.  
[Web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) is the only pure javascript way to enable a separate thread that does work.  

Crossing thread barriers always have a cost.  
Performing large operations on your UI thread blocks any other UI updates from happening, causing [jank](http://jankfree.org/).

Q: What kind of operations should be on a web worker?

1. Fetching data from a remote server
1. Processing and manipulation of data (grouping, sorting ...)
1. Expensive processing operations
1. Reading and writing on local storage
1. Image processing

## var, const, let
There is really no reason why you should still use the var keyword.    
By default, you should assign all your code to constants.  
This should be your natural habit of coding.  
Only change a const to a let when you make changes to it.  

## prototype vs class
If you are going to use object orientated programming in javascript, just use class.    
I know there are some people who just can't get over the prototype pattern, but honestly just let it go.  

## JQuery
JQuery is dead, not even boostrap wants it anymore.  
Way to go [bootstrap 5!](https://getbootstrap.com/docs/5.0/)  
Bare metal javascript is the only way to go.  

## Loop performance
Though there are a number of loops you can perform in javascript,   
the following notes look at looping from a performance perspective.  
The browser vendors are making continues improvements on this but as a rule follow these.  

<strong>AVOID</strong>
1. [for...in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in) 
   
<strong>USE</strong>
1. [for](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for)
1. [for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 
1. [forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

## Static class functions
Static functions on a class is stateless.  
They don't need the instance of the class to exist for you to use them.
They are very useful and should be considered in your code if the scenario calls for it.

### Async object creation
Working with classes there are some times you want to create a class that during it's construction requires async operations to fire.
You want to wait until the construction is done but constructors are not async.

A simple way to do this is using a convention.  
I am borrowing this convention from the Rust programming language. Thanks rust-afarians.  
The convention is that the class has a factory function called "new".  
This function creates the object and performs the async initialization.  

```js
class Person {
    static async new() {
        const result = new Person();
        // ... async stuff
        return result;
    }
}

const person = await Person.new();
```

What I like about this syntax is that it is clean and still descriptive about the intent.

### Using classes to act like namespaces
This is by no means true namespaces, but we want to use a class to group some functions.  
So before we even start I must admit that you can just export these as normal functions.  
The reason why we would want to do this is for making the code more readable so that we can use the class name as a descriptive context.

<strong>function examples</strong>

```js
// This name is not bad
export function createColumns(descriptor) {
    
}

// This name is not great but it is descriptive to what it does
export function createColumnsFromDom(element) {
    
}
```
<strong>namespaced version</strong>
```js
export class Columns {
    static async new(descriptor) {
        // ... create the columns and return it
    }
    
    static async from(element) {
        // ... create the columns using dom elements as the descriptor
    }
}

// compare these usecases compared to the function versions
const c1 = Columns.new(descriptor);
const c2 = Columns.from(element);
```

This all comes down to preference.  
Using normal functions vs static functions, I am sure there is someone who feels strongly in either direction.  
Personally I say, use what makes most sense for what you are doing.

If you need a descriptive context to make your code better maintainable then use the static function else just use normal functions.

### Measuring time
Use the [performance api](https://developer.mozilla.org/en-US/docs/Web/API/Performance) for measuring time.

## Strong concatenation
There are only two ways you should be doing string concatenation.

1. using arrays to store the values and then [join](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join) it
1. using [template strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

Though there are exceptions to the rule, the rule is "never use + to concatenate strings".

## Checking for null and undefined
Unassigned values can have the value undefined or null.  
When you check if the value is assigned you need to make sure it's not null and not undefined.

```js
if (value !== null || value !== undefined) {
    // perform action
}
```

A better way to deal with this is not checking the type.

```js
if (value != null) {
    // perform action
}
```

This catches both undefined and null.

## Redundant if statements

Beware of if-else statements, more often than not there is a better way of dealing with it.  
One such example is using a [conditional operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator);

<strong>old way</strong>
```js
let value;
if (condition === true) {
    value = 10;
}
else {
    value = 20;
}
```

This can be made better by throwing away the else in favor of a default value.

```js
let value = 10; // logical default
if (condition === false) {
    value = 20;
}
```

<strong>new way</strong>
```js
const value = condition ? 10 : 20;
```

There are times when you want to create an object if it does not exist.  

<strong>old way</strong>
```js
if (obj != null) {
    obj = new MyObject();
}
```
<strong>new way<strong>
```js
obj = obj || new MyObject();
```

The or operator will catch both null and undefined scenarios.  
Should the value is not set it will create a new instance of MyObject.

## Animation
For the most part animations should take place in a [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) function.  
This is by no means a silver bullet.  
You can still lock your UI thread with long-running tasks even if it is in a requestAnimationFrame.

Beware the following code.

```js
class Animator {
    _doSomething() {
        requestAnimationFrame(this._doSomething)
        this.x = this.calculateX();
    }    
}
```

The keyword "this" will not point to the class.  
You can fix that by putting an arrow function as the callback.

```js
requestAnimationFrame(() => {...})
```

Problem is that you now create a new function each time this runs causing the CG to kick in and causing jank.  
In such cases rather have a handler on the class that you use.

```js
class Animator {
    constructor() {
        this._doSomethingHandler = this._doSomething.bind(this);
    }
    
    dispose() {
        this._disposing = true;
        this._doSomethingHandler = null;
    }
    
    _doSomething() {
        if (this._disposing === true) return;
        requestAnimationFrame(this._doSomethingHandler);
        this.x = this.calculateX();
    }
}
```

## MouseMove event
Input events are expensive, and you want to limit your time in the event as much as possible.
The first thing you can do is to make the event handler async.1

If you need additional streamlining, only ache the input values and use it in a requestAnimationFrame loop.

```js
_mouseMove(event) {
    this.mouse.x = event.clientX;
    this.mouse.y = evnet.clientY;
}
```