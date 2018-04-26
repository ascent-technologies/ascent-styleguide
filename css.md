# CSS and Sass

*   [CSS vs Sass](#css-vs-sass)
*   [Inspinia](#inspinia)
*   [CSS formatting](#css-formatting)
    *   [Style, spacing, and such](#style-spacing-and-such)
    *   [Specificity](#specificity)
        *   [The "inception" rule](#the-inception-rule)
    *   [Ordering content within a selector](#ordering-content-within-a-selector)
        *   [Ordering CSS properties](#ordering-css-properties)
        *   [Ordering other elements](#ordering-other-elements)
*   [SMACSS and stylesheet organization](#smacss-and-stylesheet-organization)
    *   [Layout](#layout)
    *   [Modules](#modules)
    *   [States](#states)
    *   [Choosing the right file structure](#choosing-the-right-file-structure)
    *   [SMACSS Gotchas](#smacss-gotchas)
*   [Sass sugar: use wisely](#sass-sugar-use-wisely)
    *   [@extend](#extend)
        *   [When to use](#when-to-use)
        *   [When to avoid](#when-to-avoid)
    *   [@mixin/@include](#mixininclude)
        *   [When to use](#when-to-use-1)
        *   [When to avoid](#when-to-avoid-1)
    *   [Variables](#variables)
*   [References](#references)

## CSS vs Sass

We prefer the use of Sass over vanilla CSS.

## Inspinia

The customer app's base styles are built on [the Inspinia admin theme](http://webapplayers.com/inspinia_admin-v2.6/index.html).

## CSS formatting

### Style, spacing, and such

*   Use `lisp-case` for ID and class names.

*   Use soft-tabs with a two space indent.

*   Do not use the inline `style` attribute in your HTML. All element styles should live in CSS files.

*   Put spaces after `:` in property declarations, and before `{` in rule declarations.

    ```sass
    // bad
    .my-class{
      text-size:14px;
    }

    // good
    .my-class {
      text-size: 14px;
    }
    ```

*   Put line breaks between [rulesets](https://developer.mozilla.org/en-US/docs/Web/CSS/Syntax#CSS_rulesets).

    ```sass
    // bad
    .my-class{
      text-size:14px;
    }
    .my-class {
      text-size: 14px;
    }
    ```

*   When grouping selectors, put individual selectors on a new line.

    ```sass
    // bad
    #header, #footer {
      width: 960px;
    }

    // good
    #header,
    #footer {
      width: 960px;
    }
    ```

*   Avoid single line rule declarations. Each property should appear on its own line.

    ```sass
    // bad
    .closed { display: none; }

    // good
    .closed {
      display: none;
    }
    ```

*   Prefer a `box-sizing` value of `border-box`, as opposed to CSS' default value of `content-box`.

    _The customer app, by default, implements `box-sizing: border-box` on all elements.

    Using `border-box`, the width and height properties include the padding and border, but not the margin.
    Padding and border will be _inside_ of the box.

    With `box-sizing: border-box`, the following example results in a an element with a width of 350px:

    ```sass
    .box {
      box-sizing: border-box;
      width: 350px;
      padding: 0 10px;
    }
    ```

*   Use hex color codes unless using rgba(). Do not use named colors like `white`, `red`, etc.

*   Use `//` for comment blocks (instead of `/* */`). When compiling, single line comments are stripped from the file, and multiline comments are not.

*   Avoid specifying units for zero values. ex: `margin: 0;` instead of `margin: 0px;`.

*   Write `lisp-case` for any Sass sugar (e.g., placeholders, mixins, and functions). 

### Specificity

*   When styling a component, start with a base namespace to prevent selector collisions. Often the 
controller/action that the CSS is being written for 
is a good choice, e.g., `.item_show`. If the CSS is namespaced within a module, it is generally recommended 
that the module have a name that is three words or longer to avoid collisions.

*   Prefer direct descendant selectors (`>`) by default.

    ```sass
    ul {
      > li { ... }
    }
    ```

*   Use as little specificity as possible within your base namespace (the namespace will ensure that your selector are sufficiently distinct).

*   If you must use an ID selector you should have no more than one in your rule declaration. A rule like 
    `#header #quicksearch { ... }` bumps up the specificity of the selector, making it more 
    difficult to override styles later if necessary.

#### The "inception" rule

Avoid unnecessary nesting in SCSS. The selectors for your rulesets should not be more than four levels deep. 

This reduces the difficulty of overriding styles, and simplifies the parsing of SCSS hierarchies.

### Ordering content within a selector

#### Ordering CSS properties
When writing a [ruleset](https://developer.mozilla.org/en-US/docs/Web/CSS/Syntax#CSS_rulesets), 
give precedence to rules that affect positioning and appearance.  Follow these guidelines for ordering properties:

1.  The `content` property
2.  Box
3.  Border
4.  Background
5.  Text
6.  Other

**Box** includes any property that affects the display and position of the box such as `display`, `position`, `top`, `height`, `z-index`, `flex-direction`.

**Text** includes any CSS properties that affect the display of the type.

Anything that doesn’t fall into any of the above categories gets added to the end.

```sass
.my-class {
  display: inline-block;
  width: 50%;
  margin: 0 auto;
  padding-top: 10px;
  border: 1px solid #999;
  background-color: #ccc;
  color: #444;
  font-weight: bold;
}
```

#### Ordering other elements

Content within a selector should appear in the following order:

1. Sass `@extend` directives
2. Single line `@include` directives
3. Properties applicable to this selector
4. Block  `@include` directives
3. Pseudo elements (`&::before` and `&::after`)
4. Parent-referencing selectors: `&`
5. Child class selectors
6. HTML tag selectors

Think of the order in terms of semantic relevance to the selector.
The more closely related to the base selector, the closer to the top the content should appear.

```sass
.main-navigation {
  width: 75%;
  background-color: $red;

  @include media(480px) {
    width: 90%;
  }

  &::after {
    content: ""
    display: inline-block;
    height: 100%;
    vertical-align: middle;
  }

  &.is-open {
    display: block;
  }

  .link-to-home {
    @extend %nav-link;
    @include main_navigation_sprite(home);

    padding-left: 20px;
  }

  p {
    margin: 0;
  }
}
```

## SMACSS and stylesheet organization

We use CSS concepts adapted from [SMACSS](https://smacss.com).

SMACSS means "Scalable and Modular Architecture for CSS". The key words there are "scalable" and "modular". The goal of
SMACSS is to help provide a flexible process to more simply organize and maintain _reusable_ CSS "modules" within our apps.

### Layout

Layout refers to the major, broader components of a page (the minor components are "modules").
They are used to lay out sections of the page, and occur **exactly once** inside a page.

Layout elements are commonly styled using ID selectors. _This is about the only time styling ID selectors is appropriate_.

Examples of layout elements are:
*   `#header`
*   `#footer`
*   `#main-navigation`
*   `#primary-content`
*   `#secondary-content`

### Modules

Modules are discrete components of a page. They are things like navigation bars, sections on a page, lists, dialogs, widgets, and so on.

*   When defining the ruleset for a module, prefer class names over element selectors as much as possible.

*   Nested elements within a module should use the last word or meaningful phrase of the parent element as a prefix.
    This creates highly semantic, self-documenting, self-contained modules, while keeping class name length reasonable.

    ```html
    <div id="mobile-header">
      <div class="mobile-header-top-bar"> ... </div>
      <div class="mobile-header-browse-categories">
        <div class="browse-categories-toggle-container"> ... </div>
        <div class="browse-categories-content"> ... </div>
      </div>
    </div>
    ```

### States

A state is something that augments and overrides other styles.
For example, an accordion section may be in a collapsed or expanded state. A message may be in a success or error state.
States, in most cases, should be prefaced by `is_` so as to reduce their likelihood of collision with generically named elements.

*   State styles can apply to both layouts and modules.

    ```html
    <div id="footer" class="is-hidden"> ... </div>

    <div class="message error">
      There is an error!
    </div>

    <div class="secondary-navigation is-open"> ... </div>
    ```

*   State styles indicate a JavaScript dependency.

    States are applied to elements to indicate a change in state while the page is running.
    For example, clicking on a tab will activate that tab. Therefore, an `is_active` class is appropriate.

*   Define state styles as parent-referencing children of the module.

    ```sass
    .secondary-navigation {
      &.is_open {
        display: block;
      }
    }
    ```

### Choosing the right file structure

Each unique module/component should live in its own partial in `app/assets/stylesheets/components`.

For example, the module `.secondary_navigation` would live in `app/assets/stylesheets/components/_secondary_navigation.scss`.

The goal of this type of organization is to allow for quick navigation within the IDE to the exact location of the CSS
that defines the module. If I see the `#mobile_header` module on a page, I know I can go directly to `_mobile_header.scss`
to view the module's styles.

### SMACSS Gotchas

It's easy to get carried away with SMACSS and create new modules for every element. Keep in mind, the "M" in SMACSS means
"modular". Before defining a new module, ask yourself if there is an existing component that you can reuse or extend.  

## Sass sugar: use wisely
Sass provides us with a lot of powerful tools that, when not used wisely, can cause a lot of confusion and make our CSS difficult to maintain.

In general, it's best to avoid using Sass sugar unless you have a good use case for it and understand its purpose.
If you are thinking of using one of these concepts and find yourself unsure if it's the best approach, it is preferable to stick with vanilla CSS.
There are, however, many effective uses of these Sass tools in our code. The following guidelines will help you understand when it's best to use Sass sugar and when it's best to skip it.

### @extend
`@extend` works with `%placeholder-selectors` to keep CSS DRY as well as reduce the size of our compiled CSS files.  When content
from a `%placeholder selector` is extended to multiple selectors, those selectors will be grouped together in the compiled CSS.

```sass
// Sass file
%message-box {
  padding: 10px 0;
  border: 1px solid;
  background-color: #ccc;
}

.error-message {
  @extend %message-box;

  border-color: $red;
}

.notice-message {
  @extend %message-box;

  border-color: green;
}
```

```css
// Compiled CSS
.error-box, notice-box {
  padding: 10px 0;
  border: 1px solid;
  background-color: #ccc;
}

.error-message {  
  border-color: $red;
}

.notice-message {
  border-color: green;
}
```
#### When to use
`@extend` is best used in a single file when you have multiple selectors that need to reuse a long chunk of properties.  This will reduce the amount of repeated CSS in the file, making it easier to read and maintain.

**Example of effective @extend usage:**
```sass
%member-background-colors {
  &.silver::before { background-color: $membership-silver-color; }
  &.gold::before { background-color: $membership-gold-color; }
  &.platinum::before { background-color: $membership-platinum-color; }
  &.titan::before { background-color: $membership-titan-color; }
}

.membership-home-container {
  &.top {
    @extend %member-background-colors;
  }
}

.membership-home-checklist-container {
  .stylized-checklist-item-text-container {
    &.complete {
      @extend %member-background-colors;
    }
  }
}
```

#### When to avoid
Do not use `@extend` for short, frequently-used properties as it creates unnecessary confusion and style sharing. 

Avoid using `@extend` for placeholders across different files as this can make it hard to trace down styles for editing and debugging.

### @mixin/@include
Mixins are handy because they can take variable input, which makes them highly customizable.
When you want to use a `@mixin`, you `@include` it in the selector.

Typically use mixins to define a set of rules in one file that we can make use of in other areas.
They are especially useful for generic styling (buttons, pagination) that, when updated, needs to be updated the same way in multiple locations on the site.

#### When to use
Use `@mixin` for generic styles that will be repeatedly used across files and, when updated, will need to be updated in the same way in multiple areas.

Keep the contents of mixins as compact and as flat as possible to allow for easy reuse and overrides.

**Example of effective @mixin/@include usage**
```sass
@mixin media($size) {
  @media only screen and (min-width: $size) {
    @content;
  }
}

.booth-vacation-banner {
  width: 100%;

  @include media(1160px) {
    max-width: 1160px;
  }
}
```

#### When to avoid
Avoid mixins for long blocks of styles that aren't repeated very frequently or will need to be updated separately.

Avoid using mixins excessively as it spreads out code and can make it difficult to find in the source.

### Variables

Variables are a Sass paradigm that can benefit the greater good by defining regularly-used values such as 
standard colors and font sizes. 

*   Put all variables at the top of your Sass file before any placeholder selectors or style declarations.

*   If the variable is applicable to multiple modules across the entire site, place the variable in `app/stylesheets/partials/_variables.scss`.

*   Using variables is encouraged for repeated values like width, height, font-size, color, etc.

    Variables begin with dollar signs, and are set like CSS properties:

    ```sass
    $width: 5em;
    ```

    You can then refer to them in properties:

    ```sass
    .main {
      width: $width;
    }
    ```

## References

*   [CSS on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS)
*   [SMACSS](https://smacss.com)
*   [Sass Basics](http://sass-lang.com/guide)
*   [The Sass Way: Undestanding Placeholder Selectors](http://www.thesassway.com/intermediate/understanding-placeholder-selectors)
*   [The Sass Way: Leveraging Sass Mixins for Cleaner Code](http://www.thesassway.com/intermediate/leveraging-sass-mixins-for-cleaner-code)
*   [Should you use a Sass mixin or @extend?](https://roy.io/should-you-use-a-sass-mixin-or-extend/)
