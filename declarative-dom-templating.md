# Declarative DOM Templating

> **NOTE:** All examples here are not intended to be proposals for the final spec, but just an attempt to capture high-level features.

Declarative DOM templating is a way to declaratively render content based on data values. This includes conditionally rendering content and iterating over data to render templates.

## Dependencies

- [DOM Parts](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/DOM-Parts-Declarative-Template.md)

## Template Proposals

### Conditional Rendering

Single condition:

```html
<template>
  <if test="{show-heading}">
    <h3>My Progress</h3>
  </if>
  <div
    role="progressbar"
    aria-valuemin="0"
    aria-valuemax="100"
    aria-valuenow="{percentage}"
  >
    <div id="bar" style="width: {percentage}%"></div>
    <div id="label"><slot></slot></div>
  </div>
</template>
```

Multiple conditions:

```html
<template>
  <choose value={type}>
    <when test="date">
      <my-date-picker></my-date-picker>
    </when>
    <when test="range">
      <div class="base">
        <div class="gutter"></div>
        <div class="progress"></div>
        <div class="thumb"></div>
      </div>
    </when>
    <otherwise>
      <input type={type} />
    </otherwise>
  </choose>
</template>
```

## Iterating Over Data

```html
<template>
  <select>
    <each select={options} item="option">
      <if test={value === option.value}>
        <option value={option.value} selected>
          {option.text}
        </option>
      </if>
      <else>
        <option value={option.value}>
          {option.text}
        </option>
      </else>
    </each>
  </select>
</template>
```

Fortunately, there is some prior art and proposals available to create ideas.

- [The existing XLST features](https://developer.mozilla.org/en-US/docs/Web/XML/XSLT)
- [Heximal](https://github.com/elematic/heximal/tree/main)
- [EPA-WG/custom-element](https://github.com/EPA-WG/custom-element?tab=readme-ov-file#template-syntax)