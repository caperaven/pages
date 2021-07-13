# Vanilla Web Components

## Introduction
This section looks at web components and provides some quick access to common strategies.

### Basic Component

```js
export class MyComponent extends HTMLElement {
    static get observedAttributes() {
        return ['data-title'];
    }

    async connectedCallback() {
        this.innerHTML = await fetch(import.meta.url.replace(".js", ".html"));
        requestAnimationFrame(() => {
            // ... do initialization
            this.isReady = true;
            this.dispatchEvent(new CustomEvent("isReady"));
        })
    }

    async disconnectedCallback() {
        // ... clean up resources   
    }

    attributeChangedCallback(name, oldValue, newValue) {
        if (this.isReady !== true) return;
        // ... react to attribute changes
    }
}

customElements.define("my-component", MyComponent);
```

```js
export class CustomElement extends HTMLDivElement {
    async connectedCallback() {

    }

    async disconnectedCallback() {
        
    }
}

customElements.define("custom-elementr", CustomElement, { extends: 'div' });
```

The above example separates the HTML from the javascript.  
This does mean that you need to download both but in  HTTP2 environment that matters little.  
What it buys you is clean separation of concern and proper minification.

When you set the innerHTML and, you do measuring queries, the DOM is not ready to provide you with accurate feedback yet.
The `requestAnimationFrame` helps with that.

Lastly you often want to perform actions after the component has been loaded and is ready.  
This could include actions such as setting properties or attributes but could also be in UI testing.  
Setting a property `isReady` to true gives an easy way to check if the component loading has completed.  
Raising a custom event allows external runtime to wait for a component to become ready before interacting on it.

This simple strategy reduces a lot of potential timing issues at load.

Making the `connectedCallback` and `disconnectedCallback` async allows you to perform better non UI blocking actions.

### Common practices

Avoid using title, id and value attributes.  
When you use the id attribute and, you have multiple instances of the component on your screen, things can get confusing.  
Using the title and value attributes often mess with screen readers.  
To solve these issues, rather use `data-` attributes. For example, data-title.

Carefully consider the HTML you use.  
People often use far more elements than required.  
Intelligent use of CSS Pseudo-elements can save you a lot of unnecessary elements.

As a rule, if you can do something in CSS, do it in CSS.  
Don't use HTML markup to perform layout that could have been done with proper CSS use.  
This often means that you need to train yourself in CSS so that you can know how and when to use it.

From a performance perspective, large quantities and deep-rooted elements should be avoided.
