# Declarative Custom Element Definition

This proposal is that we start with component the ability to declaratively define a custom element with a statically rendered template.

## Dependencies

- None

## Definition Proposals

There have been a few proposals for how the syntax of this should look.

Creating a new `definition` element:

```html
<definition name="my-element">
  <template shadowmode="closed">
    ...
  </template>
</definition>
```

Extending the existing `template` element. This example adds a `define` attribute.


```html
<template define="my-element" shadowmode="closed">
  ...
</template>
```

some active examples use web components to define new elements. This example uses `custom-element` tag, but I think this is because it was using a custom element to declaratively create other custom elements. I believe native elements avoid using a `-` in their tag names.

```html
<custom-element tag="pokemon-tile">
  ...
</custom-element>
```

I originally preferred extending the `<template>` element, but I think Justin Fagnani highlights some really important points about the syntax in [this thread](https://discord.com/channels/767813449048260658/1359217724147892446) that demonstrates the importance of using a `<definition>` tag.

## Capabilities

This initial version would be able to declaratively define and upgradable custom element that would render static markup and styles that can have an attached shadow root.

This may not seem very useful at first, but it provides some huge capabilities that teams have been looking for:

- Removes JS requirement for non-JS components
- Provide an initial render of components to reduce FOUC
- Simplify the SSR implementation story

### Non-JS Components

The ability to remove the JavaScript requirement for CSS-only components, would be a huge win for many teams trying to create performant reusable components.

#### Library Component Example

Definition:

```html
<definition name="my-badge">
  <template shadowmode="closed">
    <style>
      :host {
        --badge-fg-color: white;
        --badge-bg-color: blue;
        --badge-border-color: blue;
      }
      
      :host([hollow]) .base {
        --badge-fg-color: blue;
        --badge-bg-color: transparent;
      }
  
      :host([variant="danger"]) .base  {
        --badge-bg-color: red;
        --badge-border-color: red;
      }
  
      :host([variant="warning"]) .base  {
        --badge-fg-color: black;
        --badge-bg-color: yellow;
        --badge-border-color: yellow;
      }
    </style>
    <span class="base" part="base">
      <slot></slot>
    </span>
  </template> 
</definition>
```

Usage:

```html
<my-badge>Default</my-badge>
<my-badge hollow>Hollow</my-badge>
<my-badge variant="danger">Danger</my-badge>
<my-badge variant="warning">Warning</my-badge>
```

### Utility Component Example

This could be used for layout components that are inline or page-level.

```html
<template define="flex-box" shadowmode="closed">
  <style>
    :host {
      --flex-box-gap: 8px;
      display: flex;
    }

    :host([wrap]) {
      flex-wrap: wrap;
    }

    :host([center]) {
      justify-content: center;
      align-items: center;
    }
    
    :host([justify="space-between"]) {
      justify-content: space-between;
    }
    
    :host([justify="space-around"]) {
      justify-content: space-around;
    }
  </style>
  <slot></slot>
</template>
```

Usage:

```html
<flex-box>
  <div></div>
  <div></div>
  <div></div>
</flex-box>

<flex-box wrap>
  <div></div>
  <div></div>
  <div></div>
</flex-box>

<flex-box center>
  <div></div>
  <div></div>
  <div></div>
</flex-box>

<flex-box justify="space-between">
  <div></div>
  <div></div>
  <div></div>
</flex-box>
```

## Reduced FOUC

One of the biggest complaints I hear from teams is that there is a negative user experience from flash of unstyled content (FOUC) that is caused by the time between the initial markup rendering and when the JavaScript is loaded, the components are defined, and finally rendered.

A example we have is that we have CMSs that lazy-load our components. There is lag between when the page's content loads and when our components are loaded and rendered.

We would like to be able to pre-render all of the default contents of our components as DCEs and load them at the root of the project. When the components are used on a page, the content is immediately rendered accurately and the interactivity is added when the lazy-loader upgrades the DCE to be an autonomous custom element (if needed).

## SSR

In order to support SSR with web components, teams are dependent on their libraries or framework to provide an integration for it for each framework.

They often also require specific component configurations and restrictions on how components can be authored.

DCEs could greatly simplify and standardize the SSR implementation for a more framework agnostic approach to pre-rendering components and rendering.

Even if we needed to add all of the DCEs to a component wrapper so they could be SSRed into the root of the project, this would be significantly simpler that the existing solutions.

```jsx
// JSX environments like react and Solid.js
export default function MyDCEComponents() {
  return (
    <>
      <definition name="my-avatar">
        ...
      </definition>
      <definition name="my-badge">
        ...
      </definition>
      <definition name="my-button">
        ...
      </definition>
      ...
    </>
  )
}
```

```html
// Vue.js
<template>
  <definition name="my-avatar">
    ...
  </definition>
  <definition name="my-badge">
    ...
  </definition>
  <definition name="my-button">
    ...
  </definition>
  ...
</template>
```

## Existing Examples

- [WICG Proposal](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/Declarative-Custom-Elements-Strawman.md)
- [EPA-WG/custom-element](https://github.com/EPA-WG/custom-element?tab=readme-ov-file#template-syntax)
- [Heximal](https://github.com/elematic/heximal/tree/main?tab=readme-ov-file#components)