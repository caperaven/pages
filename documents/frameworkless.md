# Framework-less

## Introduction
In today's development environment it's all about what framework you use.  
There are some real zealots out there also.  
Using frameworks can be problematic though and is something you need to measure the cost on.

For example, once upon a time, web development was at best difficult.    
The platform and language was not mature, and it was limiting in options on how you could build enterprise applications.  
Some libraries rose up to the challenge and birthed new ideas on the web such as components and templates.  
If you wanted a truly rich experience you had to use browser plugins such as flash or silverlight.

Both flash and silverlight has come to their end of life.  
If you have invested in either of these you would be facing a rewrite of your application.  
Along with these, frameworks like JQuery is also dead.  
It is not that these frameworks were bad, they are just not needed in the modern web anymore.

## Javascript frameworks
Introducing the javascript framework.  
What framework do you need to use and why?  
I am not going to answer the what, but I will ask "why"?  

The simple answer is that frameworks save you time.  
Perhaps they do, perhaps they don't, one thing I do know is that I have had many interviews with people who can't develop a single thing unless they are in their framework.
This is not a good state to be in. There are people who can work with or without a framework and understand how browsers work. They are however the minority.

Frameworks are thus time saving crutches.  
What makes a framework time saving?  
The simple answer for this is, boilerplate.  
Boilerplate like what?  
Well things like components and templates infrastructure.

Pick a framework, chances are that they have or had some opinion on how components and templates should be implemented.  
All frameworks are opinionated and if you agree with that opinion, by all means, use that framework.

The thing I don't like about frameworks is that it creates a dependency on that framework.  
This dependency is not always a good thing.  
If you moved from Angular 1 to Angular 2, you had to rewrite all your code again.  
There were some tools to help you transition but, they might as well not have bothered.
React used to have some real dodgy user agreement clauses that just shouted stay away.  
Fortunately they changed it but, many people happily used it without knowing the trap they were sitting on.
These are the worst kind of cases, in reality, when the framework changes direction you have no say it in. 
Follow or rewrite.

## Bare metal
The browser features has mature a lot.  
Javascript features has taken us out of the stone ages and, some of the best features of the past are now native browser features.
[Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) are now a common development browser feature in all browsers.
[Templates](https://www.w3schools.com/tags/tag_template.asp) are common in all browsers.

I am of the opinion that vanilla web components is the future safe investment.  
Supplement this with libraries and, you are off to the races.

## Libraries
One of the greatest drawbacks of libraries is the module system.  
There are still far to many vendors who rely on technologies like webpack and as a result bastardize [ES6 modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules).
A lot of the libraries are old and not maintained to stay up to date with modern web development.  
There are some libraries that do make an effort to modernize, for example [3djs](https://d3js.org/).

## Modern web development requirements
Building a new modern application there are often a set of common features that need to be in place.

1. Router
1. View Manager
1. Binding Engine
1. Set of basic components like (menu, toolbars, containers)

Having a reliable set of vanilla web components are important so that you have a dependable set of features that include things like [web accessibility](https://www.w3.org/WAI/fundamentals/accessibility-intro/)
There is a whole range of libraries that you can mix and match to help you achieve these parts.  
Once you have decided what parts you want to use, it's a good idea to create a github template so that next time you want to create a new application you can just use the existing template.

One of the most important choices you can make is the binding engine.

## Binding engine

A binding engine is the most important time saving technologies of the modern web.  
Binding engines put information on the screen and helps with interactions on the screen.  
Good binding engines will dramatically reduce the amount of javascript you write and has a flexible expression syntax.

This is also at the heart of every framework.  
The good news is that don't need to commit to a framework to use a good binding engine.  
There are enough binding engine libraries for you to choose.  

Like frameworks, binding engines have opinions.  
In particular those opinions are around how the user defines intent, what kind of expressions are supported.  

[crs-binding](https://github.com/caperaven/crs-binding) is build from the ground up with our particular needs as its primary goal.  
It does not try and be the all or nothing for every scenario but is very flexible and has a bare metal opinion on expressions.

So what do I mean by bare metal expressions.  
As far as possible we try and use stock standard javascript based expressions.  
These [expressions](https://github.com/caperaven/crs-binding-documentation/blob/master/3.%20binding-expressions.md) are sanitized to ensure context scoped expressions.  

It also deals with features such as:

1. View base to simplify [view management](https://github.com/caperaven/crs-binding-documentation/blob/master/1.%20viewbase.md)
1. [Bindable element](https://github.com/caperaven/crs-binding-documentation/blob/master/2.%20bindable-element.md) for using crs-binding in components easily
1. Event aggregation to allow pub-sub type communication in the application.
1. Extends standard template features to allow HTML injection
1. ... [and many more](https://github.com/caperaven/crs-binding-documentation)

[Here](https://crs-binding-examples.web.app/#input-form) can see a simple example app using crs-binding.

## Bare metal applications

So you have some great libraries for navigation, binding, components, ext..., why do you need a framework?  
The answer is, that you don't.  
One of the great options on frameworks like VueJs is the theme engine, but you get libraries for that too.

The one thing you can't live without however is a good set of components.

## Components / UI parts

The one thing you can't do without is a great set of components.  
You can write them yourself but there are often some good ones out there just ready for you to use.
Here are some examples:

1. [material.io](https://material.io/components?platform=web) - google's material design components
1. [bootstrap 5](https://getbootstrap.com/docs/5.0/components/)
1. [material components](https://github.com/material-components/material-components-web-components)
1. [materialize](https://materializecss.com/)

Just note that any library and component library you use will have some cost.  
Make sure you understand the size of the payload and, the performance impact of your choices.
I would recommend starting with [material.io](https://github.com/material-components/material-components-web) as they are build to be used [individually](https://github.com/material-components/material-components-web/tree/master/packages).

## Conclusion

For your next long term project, modern web technology without frameworks is ready to help you develop great interactive, responsive, web accessible applications.