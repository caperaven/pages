# Dynamic module loading 

## Introduction
The purpose of this document is to explain dynamic module loading by example.  
Let us first define what we mean by dynamic module loading.  

A javascript file is a module.  
Using ES6 you can export features such as functions, classes, constants and defaults.  
When you use the `import` function you get back a module that contains the above exported features.

A lot of people rely on build tools like webpack to determine what code gets loaded.  
I am personally not a fan and like to load modules depending on behaviour and conditions.  

The strength of dynamic loaders is that you can separate concerns properly and load the features only when you need it.
"if and only when I need this feature, import the module and execute the feature".

## Scenario
The scenario I am going to use here is based on a grid component I am writing.

I have a grid and, the grid supports three different paging strategies.  
You can only run on strategy at a time.  
This means we can use proper separation of code and define a convention around the paging strategy api.  
You only want to load the module you need at the time that you need it.  
This is driven through a data- attribute on the custom element called `data-type`.

If the type is "static", load the "static.js" file.  
If the type is "dynamic", load the "dynamic.js" file.  
If the type is not defined, load the "virtualized.js" file.

Since there is some conditional logic we want to be able to check on the type we need an enum to define the type options.

```js
export const RowsType = Object.freeze({
    STATIC      : "static",
    DYNAMIC     : "dynamic",
    VIRTUALIZED : undefined
})
```

We also may want to extend this in the future and don't want to get stuck in an if / else or switch overhead that needs to be maintained.
We thus create an object literal based on your enum that defines what enum value matches what module to load.

```js
export const PagingModules = {
    [RowsType.STATIC]       : "./static.js",
    [RowsType.DYNAMIC]      : "./dynamic.js",
    [RowsType.VIRTUALIZED]  : "./virtualized.js"
}
```

Now if I need a new scenario I add it to the enum and the PagingModules.  
The execution code never needs touching again.

Talking of the execution code, the modules are loaded like this.

```js
grid.rowsManager = new (await import(PagingModules[grid.dataset.type])).default(grid);
```

<strong>Note:</strong>
1. The data-type matches the values expected on the num.
1. Each module exports its operational class as a default.

## Conclusion
Dynamic module loading:

1. load only what you need
1. if you need it
1. when you need it
