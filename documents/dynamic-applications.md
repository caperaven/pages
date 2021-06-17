# Dynamic applications

## Introduction
Q: "Dynamic Applications", what do we mean by that?

Dynamic applications are applications that can change.  
This would include:

1. Creating a screen based on a profile schema at runtime.
1. Allowing custom actions based on a profile schema.
1. Allow custom changes on the screen based on expressions.

This allows the end user to customize their screens to fit their needs and enable actions as they have defined it.

## Background 

I worked on a product several years ago where the screens contained all the information for a given subject.  
The work order screen would be an example of that.  
The screen had lots of tabs and each tab had groups and input fields.  
It was a static screen and had every possible field you could potentially need.
Users often got confused as to where to find information they cared about.

We added a custom tab as a generic feature on screens where people could put fields they care about in the sequence they cared about it.
This quickly became the most valued feature in the application.

This got me thinking, what if the entire application could work like that.  
Not only giving you the ability to update a single tab but to provide a form designer where you can customize a screen to your hearts content.
Add tabs that you feel needs to be there with the fields on it that you want.
Remove tabs you did not use or fields on groups you did not use.
In short customize the screen as to how you need it.

Save this customization as a profile so that you can view at will any variation of the screen by switching profiles.

## Run time vs Build time
The above example is more of a runtime scenario, but can I use the same idea for build time and why would I want to do that?

Imagine you did not define your UI in HTML, but instead in a JSON document.  
You did not have a single parser but several.  
Such parsers could include:

1. HTML parser - generate HTML views from the JSON definition
1. XAML parser - generate XAML views from the JSON definition
1. IOS parser - generate code that builds view in swift

Those are some examples of what you could do.  
Another example would be to generate the HTML views based on the framework you want to target or what css library you want to apply.

That is what dynamic applications in concept offers.

## What do you need to make something like this work

1. JSON schema defining your UI
1. A parser that will process the schema
1. Providers that will translate particular parts

For example, I can have a bootstrap parser that has providers for different element types.
One such provider knows how to parse buttons. The parser processes the JSON it picks up an element type of "button".
The parser then sends the schema part to the button provider to process and generate the required markup.

## What is the benefits of this

### Runtime benefits
1. Profile driven, user defined screens
   
### Build time benefits
1. Flexible, target generation of screens.
1. JSON is more accessible to non developers. 
1. Reuse the same schema to generate screens for any platform.

### Schema library
[crs-schema](https://github.com/caperaven/crs-schema) is a library that helps you in this regard.

The HTML [providers](https://github.com/caperaven/crs-schema/tree/master/src/html/providers) shipped by default only caters for a small amount of scenarios.  
You will notice that there are no providers for common HTMLElements.  
When the HTML parser process the schema and does not find an appropriate provider it assumes the element defined is to be generated as is.  
For example, if you have a definition for a "h2" element and, it does not find a "h2" provider it will generate an element with the tag of "h2".

Thus, you only need to create providers for non-standard items.
Let us say that we have a generic term for a grid that we use in the schema.
When the parser sees "grid" it should actually generate a HTML element of "my-grid", a custom provider for this is required.

## Execute intent
Generating custom UI is not the only part of dynamic applications.  
What if I want to add a button to the screen and when that button is clicked I want to execute a process or action.

This is where [process](https://github.com/caperaven/crs-process-api) and [module](https://github.com/caperaven/crs-modules) engines come into play.
crs-process-api and crs-modules allow dynamic loading and executing of intent.

The purpose of crs-modules is around loading modules as and when you need it and has a feature that extends the [crs-binding](https://github.com/caperaven/crs-binding) engine.
crs-binding is a binding engine that processes the DOM and builds expression hooks for interactions on the UI. crs-modules extends that to load files for custom elements.
This means you don't need to pre-load your component files. By combining crs-schema, crs-binding and crs-modules, you can have dynamic UI that loads its requirements as and when it needs it.
crs-modules also allows you to execute functions on registered modules. If you request a function to executed and, the module has not been loaded yet, it will first load the module and then execute the function.
Functions being called this way must use the ES6 module export feature.

A good resource on how to use these features is to look at the [tests on crs-process-api](https://github.com/caperaven/crs-process-api/tree/master/test/action-systems-tests).

### Schema structure
It is important to note that the schema structures change depending on intent of the schema.  
UI schemas look different to process schemas.

### Generate anything
Using the concept of dynamic applications you can generate almost anything.
To thus far we have spoken about generating HTML or executing processes, but in this [example](https://github.com/caperaven/crs-components/blob/master/app/graphics-program/schema.js) we generate 3d graphics and a master class.

Using the TreeJS library, this [parser](https://github.com/caperaven/crs-components/blob/master/src/gfx-providers/graphics-parser.js) evaluates the schema and with the help of registered [managers](https://github.com/caperaven/crs-components/tree/master/src/gfx-providers/managers) and [providers](https://github.com/caperaven/crs-components/tree/master/src/gfx-providers/providers) updates the scene.
A "program" class is also created that you can access to manipulate the scene.
Each manager is dynamic and only injects features on the program if that manager is made use of.  
The color manager for example adds the function "setColor" on the program so that you can programmatically change the color value for a color defined in the schema.
By "programmatically" we mean, either using javascript or through the process-api.

## Conclusion
Schema driven profiles, user customizable, interactive, extendable programs.  
Generate UI or code at runtime or build time.  
The only thing missing here is a nice user interface that allows you to tie all these things together and build your own features.  
The company I work for is building this directly into the core application but, I am thinking about building something like that in my free time when I get time.
