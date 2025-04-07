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

Extending the existing `template` element. This example adds a `define` attribute:

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

## Capabilities

This initial version would be able to declaratively define and upgradable custom element that would render static markup and styles that can have an attached shadow root.

This may not seem very useful at first, but it provides some huge capabilities that teams have been looking for:

- Removes JS requirement for non-JS components
- SSR

### Non-JS Components

The ability to remove the JavaScript requirement for CSS-only components, would be a huge win for many teams trying to create reusable components.

#### Library Component Example

Definition:

```html
<template define="my-badge" shadowmode="closed">
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

## SSR

One of the biggest complaints I hear from teams is that there is a negative user experience from flash of unstyled content (FOUC) that is caused by time between the initial markup rendering and when the JavaScript is loaded and the components are defined. Teams rely on their frameworks to server-side-render (SSR) their page content to remove this behavior.

The ability to create the default markup and styles of a component would reduce this issue by allowing content to be initially style and slotted in the right place from the start. Then, once JavaScript has been loaded, components could be upgraded.
