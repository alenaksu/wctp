---
author: Alessandro Valeri
theme: seriph
title: Web Components and Theming
titleTemplate: '%s'
background: ./images/cover.webp
highlighter: shiki
lineNumbers: true
info: null
drawings:
  persist: false
transition: slide-left
layout: cover
hideInToc: true
fonts:
  sans: Raleway
  serif: Roboto Slab
  mono: Fira Code
css: unocss
download: true
---

# Web Components and Theming

Building reusable and themeable UI components

<!--
Compound selector: A sequence of simple selectors that are not separated by a combinator
-->

---
layout: center
---

https://stackblitz.com/fork/web-comp-theming

---
layout: center
---

# What are Web Components?

---
layout: center
---

# Example: custom image carousel

Web components allows developers to extend the browser with new components

```html
<image-carousel>
    <img src="/images/image1.jpg" alt="image 1" />
    <img src="/images/image2.jpg" alt="image 2" />
    <img src="/images/image3.jpg" alt="image 4" />
    ...
</image-carousel>
```

<!--
Like the one we see here, an implementation of an image carousel that can be expressed using normal HTML markup
-->

---

# Web Components

<p>A set of Low Level APIs that enable developers to create reusable, encapsulated and single responsability HTML elements.</p>

<v-clicks>

-   ### Custom Elements

    The foundation of web components, an API that allows the user to defined fully-featured DOM elements.

-   ### Shadow DOM

    Makes it possible to create elements that have their own encapsulated styles and markup, preventing them from interfering with the rest of the page.

-   ### HTML Templates
    Provides you a way to define reusable chunks of HTML that can be easily cloned and inserted into your application.

</v-clicks>


---
layout: center
---

# What Web Components are not

<v-clicks>
	
  - ### Not a framework	
  - ### Not a rendering library
  
</v-clicks>

<!--
Not meant as a replacement for
- any framework
- rendering library

Web components just tell the browser when and where to create a component but not how
-->

---
layout: image-right
image: images/custom-elements.jpg
---

# Custom Elements

---

# Autonomous custom elements

Defining a brand new HTML element

```js {0|1,8|2,7|3-4|6|10|all}
class MyElement extends HTMLElement {
    constructor() {
        // always call super first in constructor!
        super();

        // write element functionality in here
    }
}

customElements.define("my-element", MyElement);
```

<v-click>

Over in our HTML, we use it like so:

```html
<my-element></my-element>
```

</v-click>


---

# Customized built-in elements

Enhancing an existing HTML element

```js {all|1|10|all}
class MyButton extends HTMLButtonElement {
    constructor() {
        // always call super first in constructor!
        super();

        // write element functionality in here
    }
}

customElements.define("my-button", MyButton, { extends: "button" });
```

<v-click>
  
Over in our HTML, we use it like so:
```html
<button is="my-button">Click me!</button>
```
  
</v-click>

<v-click>
  
*Not supported in Safari <twemoji-grinning-face-with-sweat />
  
</v-click>

<!--
Demo time:
- Alert button
- Prompt button
-->

---
layout: iframe
url: https://stackblitz.com/edit/web-platform-l2bhuu?embed=1&file=index.html
---

---

# Using the lifecycle callbacks

Lifecycle callbacks are a set of methods that are automatically called by the browser at specific points in the lifecycle of a Web Component

```js {1,18|1,18,2-3|1,18,5-6|1,18,8-9|1,18,11-14|1,18,16-17|all}
class MyElement extends HTMLElement {
    // Invoked each time the custom element is appended into a document-connected element.
    connectedCallback() {}

    // Invoked each time the custom element is remove from the document's DOM.
    disconnectedCallback() {}

    // Invoked each time one of the custom element's attributes is added, removed, or changed.
    attributeChangedCallback(name, oldValue, newValue) {}

    // Attributes that a component should 'observe' for changes
    static get observedAttributes() {
        return ["attrib-a", "attrib-b"];
    }

    // Invoked each time the custom element is moved to a new document.
    adoptedCallback(oldDocument, newDocument) {}
}
```

<!--
`connectedCallback`: It is typically used to perform any necessary setup or initialization. To note that this is called even when the element is moved, since the move is performed by removing and appending the element, per specs.

`disconnectedCallback`: It is typically used to perform any necessary cleanup or tear-down.

`attributeChangedCallback`: It allows developers to update the component's state or perform other actions in response to the change.

`adoptedCallback`: probably you will never have to use this callback

DEMO TIME:
- mount/unmount
- react to attribute changes
- clock element
-->

---

# Progressive enhancement

There's no need to define all in one go, but you may load your custom element when is more convenient for you.

<v-click>
  
Example:

```js {all|2|5-7|all}
// Lazy-load non critical element
requestIdleCallback(() => import("lazy-element.js"));

// Wait for the element to be defined
customElements.whenDefined("lazy-element").then(() => {
    console.log("lazy-element defined");
});
```

</v-click>

---
layout: image-right
image: images/shadow-dom.jpg
---

# Shadow DOM

---

# Shadow DOM vs Light DOM

Understanding the differences and benefits of Light DOM and Shadow DOM

<v-click>

**Light DOM** is the one you are already familiar with.

</v-click>

<v-click>
  
  **Shadow DOM** allows for encapsulated styles and markup, providing a way to create elements that are self-contained and do not affect the styling of other elements on the page. 
  
</v-click>

<v-clicks>
 
  - `Isolated DOM` A component's DOM is self-contained (e.g. document.querySelector() won't return nodes in the component's shadow DOM).
  - `Scoped CSS` CSS defined inside shadow DOM is scoped to it. Style rules don't leak out and interfer with the rest of the page.
  - `Composition` Design a declarative, markup-based API for your component.
  - `Simplifies CSS` Scoped DOM means you can use simple CSS selectors, more generic id/class names, and not worry about naming conflicts.

</v-clicks>

<!--
- `Shadow host`: The regular DOM node that the shadow DOM is attached to.
- `Shadow tree`: The DOM tree inside the shadow DOM.
- `Shadow boundary`: the place where the shadow DOM ends, and the regular DOM begins.
- `Shadow root`: The root node of the shadow tree, is a document fragment that gets attached to a “host” element.
- `light DOM vs. shadow DOM`


- https://web.dev/shadowdom-v1/#creating-shadow-dom
- https://kinsta.com/blog/web-components/#a-brief-history-of-web-components
- https://www.youtube.com/watch?v=YBwgkr_Sbx0&t=9s
- https://www.youtube.com/watch?v=DBcz_bGcHgk&t=335s
- https://www.digitalocean.com/community/tutorials/web-components-your-first-custom-element#shadow-dom
- https://web.dev/constructable-stylesheets/#:~:text=Constructable%20Stylesheets%20are%20a%20way,styles%20when%20using%20Shadow%20DOM.&text=It%20has%20always%20been%20possible,element%20using%20document.
-->

---

# Creating Shadow DOM

Calling `Element.attachShadow()` on a supported element, creates a Shadow DOM for that element and returns its shadow root.

<v-click>
  
Which elements can host a Shadow tree?
  
</v-click>

<v-clicks>
  
  - Any valid custom element
  - `article`, `aside`, `blockquote`, `body`, `div`, `footer`, `h1`, `h2`, `h3`, `h4`, `h5`, `h6`, `header`, `main`, `nav`, `p`, `section`, or `span`
  
</v-clicks>

<v-click>
  
  Exmaple:
  
```js {all|2-4|7|all}
const header = document.createElement("header");
const shadowRoot = header.attachShadow({ mode: "open" });
// header.shadowRoot === shadowRoot
// shadowRoot.host === header

// Could also use appendChild().
shadowRoot.innerHTML = "<h1>Hello Shadow DOM</h1>";
```
  
</v-click>


---

# Creating Shadow DOM for a custom element
Example: attaching a Shadow DOM to a custom element, encapsulating its DOM/CSS.

```js{all|5|6-14|all}
class CardElement extends HTMLElement {
  constructor() {
    super();

    const shadowRoot = this.attachShadow({ mode: 'open' });
    shadowRoot.innerHTML = `
        <!-- styles are scoped to card-element! -->
        <style> 
          #title { ... }
          #content { ... }
        </style>
        <div id="title">...</div>
        <div id="content">...</div>
    `;
  }
}
```

---
layout: image-right
image: images/html-templates.jpg
---

# HTML Templates

---

# Declaring templates

Templates contain fragments of HTML content that is not rendered immediately, but may be instantiated later on at runtime using JavaScript.

<v-click>

HTML templates are defined using the `<template>` element. 

```html{all|2-8|9|all}
<template id="my-template">
  <style>
    p {
      color: white;
      background-color: #666;
      padding: 5px;
    }
  </style>
  <p>My template</p>
</template>
```

</v-click>


---

# Using templates
The element and its content are not rendered in the DOM, until you reference and append it to the document.

```html{all|2|3|4|all}
<script>
    const template = document.querySelector('#my-template');
    const clone = template.content.cloneNode(true);
    document.body.appendChild(clone);
</script>
```

<v-click>
  
Creating elements from a `<template>`

```js{all|5|6-7|8|all}
class MyElement extends HTMLElement {
  constructor() {
    super();
  
    const shadowRoot = this.attachShadow({ mode: 'open' });
    const template = document.querySelector('#my-template');
    const content = template.content.cloneNode(true);
    shadowRoot.appendChild(content);
  }
}
```

</v-click>

---

# Composition and slots

Adding flexibility with the `<slot>` element

```html{0|3-7|9-10|12-15|17-18|all}
<my-element>
  #shadowroot (open)
    <!-- 
      A slot is a placeholder on your Web Component that can be filled with markup. 
      A component can define zero or more slots in its shadow DOM. 
    -->
    <slot></slot>

    <!-- Slots can be empty or provide fallback content. -->
    <slot>fallback content</slot> 

    <slot> 
        <h2>Title</h2>
        <summary>Description text</summary>
    </slot>

    <!-- Slots can be named so that users can reference them by name. -->
    <slot name="header">Default Header</slot>
</my-element>
```

---

# Example
Defining a template for a Card component

<div class="flex gap-6">

  <div v-click class="flex-1">
    
  ```html
  <template>
    <div class="card">
      <div class="header">
        <slot name="header"></slot>
      </div>
      <div class="body">
        <slot>
          <p>No Content Provided</p>
        </slot>
      </div>
    </div>
  </template>
  ```
    
  </div>

  <div v-click class="flex-1">
    
  ```html
  <card-element title="Card title">
      <h1 slot="header">Slot header</h1>

      <div>This is the card content</span>
      <div>and also this</span> 
  </card-element>
  ```
    
  </div>
  
</div>

<!--
DEMO TIME:
- Card component
-->

---
layout: image-right
image: images/theming.jpg
---

# Theming

---

# CSS Custom Properties

User-defined variables that contain specific values to be reused throughout a document.

```css{0|1-6|8-12|10|11|12-18|10,16|all}
:root {
    /** Property names that are prefixed with "--" */
    --background-color: #f8f9fa;
    --text-color: #333333;
    --spacing: 1rem;
}

body {
    /** Values can be used using the `var()` function */
    background-color: var(--background-color);
    color: var(--text-color, black);
}

body.dark {
    /** Variables can be overridden */
    --background-color: #333333;
    --text-color: #f8f9fa;
}
```

<!--
- The :root CSS pseudo-class matches the root element of a tree representing the document. In HTML, :root represents the html element and is identical to the selector html, except that its specificity is higher.
  
- Property names that are prefixed with --

- Follow the same inheritance and cascade rules as other CSS properties

- Variables defined in a more specific selector will override those defined in a less specific selector
-->

---

# The `::part` pseudo-element

Represents any element within a shadow tree that has a matching `part` attribute.

<v-click>

Assigning a `part` attribute to style individual parts of a web component

```html{all|3,4|all}
<card-element title="Card title">
  #shadow-root (open)
    <div class="title" part="title">Card title</span>
    <div class="content" part="content">This is the card content</span>
</card-element>
```

</v-click>

<v-click>

  Using the `::part` pseudo-element, it is possible to style the elements that have been exposed outside the component via the `part` attribute

  ```css
  card-element::part(title) {
      color: blue;
      margin: 1rem;
      font-size: 22px;
  }
  ```

</v-click>


---

# The `::slotted` pseudo-element 
Represents any element that has been placed into a slot inside an HTML template 

```css{0|1-3|5-8|10-13|all}
::slotted(<compound-selector>) {
  /* ... */
}

/* Selects any element placed inside a slot */
::slotted(*) {
  font-weight: bold;
}

/* Selects any <span> placed inside a slot */
::slotted(span) {
  font-weight: bold;
}
```

<v-clicks>

* Only works when used inside CSS placed within a shadow DOM

</v-clicks>

<!--
Compound selector: a sequence of simple selectors that are not separated by a combinator
-->

<!--
# Constructible Stylesheets

Provides a way to create and share reusable CSS styles to multiple Shadow Roots or the Document easily and without duplication.
-->