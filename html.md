# Semantic HTML

*   [Coding Style](#coding-style)
*   [IDs and Classes](#ids-and-classes)
*   [Boolean Attributes](#boolean-attributes)
*   [Data Attributes](#data-attributes)
*   [Lean Markup](#lean-markup)
*   [References](#references)

## Coding Style

The core goal to keep in mind when writing HTML is:

**Your HTML should reinforce the semantics, or meaning, of the information in the webpage rather than merely be used to define its presentation or look.**

*   HTML tags names should be lowercase.

*   Use `lisp-case` for ID and class names.

*   `<span>` and `<div>` hold no semantic value alone. These tags should _always_ have a descriptive `class` or `id`.

*   Paragraphs of text should always be placed in a `<p>` tag.

*   Never use `<br>` tags for spacing between elements.

*   Items in list form should always be in `<ul>`, `<ol>`, or `<dl>`.

*   `<strong>` and `<em>` have more semantic value than `<b>` and `<i>`.

    The `<strong>` element represents text of certain importance, `<em>` puts some emphasis on the text.
    The `<b>` element doesn't convey such special semantic information; use it only when no others fit.

*   `<table>` should only be used for tabular data on the page, and _never_<sup>1</sup> for layout.

    <sup>1</sup> <small>Unless you're marking up an email template, and then you _should_ use tables for the layout.</small>

*   It is highly recommended that every form input that has text attached should utilize a `<label>` tag.

*   `<label>` elements must always<sup>2</sup> include a `for` attribute for their corresponding `<input>`, `<textarea>`, or `<select>` element.

    We should ​*always*​ be using labels with our form input elements.
    A `<label>` tag is semantic markup that adds a wrapper for styling, and most importantly, it’s necessary in order to properly follow accessibility guidelines.

    ```html
    <!-- bad -->
    First name: <input type="text" name="first_name">

    <!-- bad -->
    <label>First name:</label>
    <input type="text" name="first_name">

    <!-- good -->
    <label for="first_name">First name:</label>
    <input type="text" id="first_name" name="first_name">

    <!-- also ok, but not preferred -->
    <label>
      <input type="checkbox" name="accept_tos" value="1">
      I have read and accept the terms of service.
    </label>
    ```
    
    <sup>2</sup> <small>Unless your `<label>` wraps the `<input>` element.</small>

*   Radio and checkbox inputs must always be paired with a corresponding `<label>` element.

    ```html
    <!-- bad -->
    <input type="checkbox"> I agree

    <!-- good -->
    <input id="accept_terms" type="checkbox">
    <label for="accept_terms">I agree</label>

    <!-- also ok, but not preferred -->
    <label for="accept_terms">
      <input id="accept_terms" type="checkbox">
      I agree
    </label>
    ```

*   Always put quotes around attributes.

    ```html
    <!-- bad -->
    <div class=foo> ... </div>

    <!-- good -->
    <div class="foo"> ... </div>
    ```

*   Avoid trailing slashes on self-closing elements. For example: `br`, `hr`, `img`, `input`, `meta`.

*   Do not use the inline `style` attribute. All element styles should live in CSS files.

## IDs and Classes

*   Prefer using classes over IDs in your markup.

*   IDs should only be used for elements that occur exactly once inside a page. When in doubt, use a class name.

*   IDs should only be used for layout-related elements like `#header`, `#main`, `#footer`, `#sidebar` and Javascript hooks when necessary.

*   IDs and classes should have descriptive names that add semantic value. Names should describe the element, and not a design concept.

    ```html
    <!-- bad -->
    <ul class="float_left"> ... </ul>

    <!-- good -->
    <ul class="main_navigation"> ... </ul>
    ```

*   Think of logical sections of the page as "modules", and name the related elements accordingly.

    Nested elements within a module should use the last word or meaningful phrase of the parent element as a prefix.
    This creates highly semantic, self-documenting, self-contained modules, while keeping class name length reasonable.

    ```html
    <div class="heading">
      <ul class="heading_navigation">
        <li class="navigation_item">
          ...
        </li>
      </ul>
    </div>
    ```

*   Useful common patterns for class names:
    *   `ul`: `*_list`. ex: `<ul class="product_list">`
    *   `li`: `*_list_item`. ex: `<li class="product_list_item">`
    *   `a`: `link_to_*`. ex: `<a href="#" class="link_to_home">`

## Boolean Attributes

Boolean attributes - like disabled and checked - don’t require a value to be set.
Their presence indicates a true value, and absence a false value.

```html
<input type="checkbox" checked disabled>
````

**Note:**
Rails form helper methods will output boolean attributes in the form of: `disabled=“disabled”`.
This is acceptable when using rails helpers.

## Data Attributes

Data attributes indicate a javascript dependency. They are useful for associating some dynamically generated rails
content with an element for use in javascript.

## Lean Markup

*   Whenever possible, avoid superfluous parent elements when writing HTML.

    ```html
    <!-- Probably superfluous -->
    <span class="avatar">
      <img src="...">
    </span>

    <!-- In most cases, this is probably better -->
    <img class="avatar" src="...">
    ```

*   Whenever possible<sup>3</sup>, do not use empty content tags solely for the purpose of styling display elements.

    Consider `background-image`; `::before` and `::after` pseudo elements; and inline images as alternatives.

    ```html
    <!-- Bad -->
    <a href="/my_cart">
      Cart
      <div class="cart_sprite"></div>
    </a>
    ```
    
    <sup>3</sup> <small>This is sometimes unavoidable with javascript libraries that need to insert dynamically generated content.</small>

*   [CSS pseudo class selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)
    like `:first-child`, `:last-child`, `:first-of-type`, `last-of-type`, and `nth-child`
    can help you style specific elements without the addition of superfluous tags or classes.

## References

*   [HTML on MDN](https://developer.mozilla.org/en-US/docs/Web/HTML)
