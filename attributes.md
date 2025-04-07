# Attributes and Template Binding

The next proposal is the ability to pass data into the template via attributes.

## Dependencies

- [DOM Parts](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/DOM-Parts-Declarative-Template.md)

## Definition Proposals

One of the proposals is an imperative syntax for binding attribute values to templates.

```html
<definition name="percentage-bar">
  <template shadowmode="closed">
    <div 
      role="progressbar" 
      aria-valuemin="0" 
      aria-valuemax="100" 
      aria-valuenow="{{root.attributes.percentage.value}}"
      >
        <div id="bar" style="width: {{root.attributes.percentage.value}}%"></div>
        <div id="label"><slot></slot></div>
    </div>
  </template>
</definition>
```

```html
<percentage-bar percentage="75"></percentage-bar>
```

Another was a declarative syntax.

```html
<definition name="percentage-bar">
  <attribute name="percentage">0</attribute>
  <template shadowmode="closed">
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
</definition>
```

```html
<percentage-bar percentage="75"></percentage-bar>
```

### Defining Attribute Type

Is there a need to specify the type of the attribute to indicate if it's a boolean or string?

```html
<definition name="my-date-picker">
  <attribute name="disabled" boolean></attribute>
  <template shadowmode="closed">
    <input disabled={disabled} />
  </template>
</definition>
```

### API Validation

With the declarative approach, a proposal that was discussed was the opportunity to provide some basic validation for attributes by defining a list of allowed values and a fallback if one of the defined values is not used.

```html
<definition name="my-date-picker">
  <attribute name="type">
    <option default>date</option>
    <option>datetime-local</option>
    <option>month</option>
    <option>time</option>
  </attribute>
  <template shadowmode="closed">
    <input type={type} />
  </template>
</definition>
```

```html
<!-- `type` would fall back to "date" because a defined value was not used -->
<my-date-picker type="range"></my-date-picker>
```

## Existing Examples

- [EPA-WG/custom-element](https://github.com/EPA-WG/custom-element?tab=readme-ov-file#attributes)
- [WICG Proposal](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/Declarative-Custom-Elements-Strawman.md)