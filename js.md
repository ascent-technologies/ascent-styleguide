# Javascript

*   [ECMAScript Support](#ecmascript-support)
*   [Semicolons](#semicolons)
*   [Naming](#naming)
*   [Variable Declaration](#variable-declaration)
    *   [Function Expressions vs Declarations](#function-expressions-vs-declarations)
*   [Spacing](#spacing)
*   [Objects and Arrays](#objects-and-arrays)
    *   [Object Property Access](#object-property-access)
*   [jQuery](#jquery)
    *   [Attaching jQuery Events](#attaching-jquery-events)
        *   [An example attaching jQuery events](#an-example-attaching-jquery-events)
    *   [jQuery Sugar](#jquery-sugar)
*   [Common Patterns](#common-patterns)
    *   [Page Initialization Pattern](#page-initialization-pattern)
    *   [Module Definition Pattern](#module-definition-pattern)
    *   [Class Definition Pattern](#class-definition-pattern)
    *   [jQuery Plugin Pattern](#jquery-plugin-pattern)
*   [References](#references)

## ECMAScript Support

Our Javascript may implement all functionality up to and including features added by ECMAScript 2016 (ES7).

## Semicolons

*   Always use semicolons.

*   Semicolons should be included at the end of function expressions, but not at the end of function declarations.

    ```javascript
    // function expression
    var foo = function() {
      return true;
    };  // semicolon here.

    // function declaration
    function foo() {
      return true;
    }  // no semicolon here.
    ```

## Naming

*   Use `camelCase` for functions and variables.

*   Use `PascalCase` for modules, classes, and constructors.

*   Use `SCREAMING_SNAKE_CASE` for other constants.

*   _Do not_ use `_` as the first or last character of a variable to indicate privacy.

*   _Do not_ prefix variables with `$` to indicate that it is a jQuery object.

*   Use descriptive names for variables and functions. Avoid single letter names.

## Variable Declaration

*   Always declare variables with `var`.

*   Avoid no-op declarations. Every variable should be initialized to some value, even if it's `null`;

*   One variable name per `var` per line.

    ```javascripts
    // bad
    var foo, bar;
    var food,
        bard;

    // good
    var foo = null;
    var bar = 0;
    ```

### Function Expressions vs Declarations

Prefer the user of function expressions over declarations.
*   https://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/

## Spacing

*   Use soft-tabs with a two space indent.

*   Use spaces around operators, after commas, colons and semicolons, after `{` and before `}`, and around array initialization.

    ```javascript
    // good
    sum = 1 + 2;
    1 > 2 ? true : false;
    foo = { one: 1, two: 2 };
    bar = [ 1, 2, 3 ];
    ```

*   No spaces after `(` or before `)`, or inside `[]` object lookups.

    ```javascript
    // good
    foo["one"];
    bar(arg);
    average = (a + b) / c;
    ```

*   Never put a space between a function name and the opening parenthesis.

    ```javascript
    // bad
    foo (arg);
    var bar = function ( arg ) {
      // ...
    };

    // good
    foo(arg);
    var bar = function(arg) {
      // ...
    };
    ```

*   No spaces before `:` in an object definition.

    ```javascript
    // bad
    foo = {
      one : 1,
      two : 2
    };

    // good
    foo = {
      one: 1,
      two: 2
    };
    ```

*   Unary special-character operators (e.g., `!`, `++`) must not have space following their operand.

*   `if`/`else`/`for`/`while`/`try` always have braces, and should usually go on multiple lines.

    ```javascript
    // bad
    if (condition) doSomething();

    // ok
    if (condition) { doSomething(); }

    // good
    if (condition) {
      doSomething();

    } else if (anotherCondition) {
      doSomethingElse();

    } else {
      otherwise();
    }
    ```

*   No filler spaces in empty constructs (e.g., `{}`, `[]`, `fn()`);

## Objects and Arrays

*   Use `{}` instead of `new Object()`.

*   Use `[]` instead of `new Array()`.

*   Use arrays when the member names would be sequential integers.

*   Use objects when the member names are arbitrary strings or names.

### Object Property Access

*   Use dot notation to access a vanilla object's property.

    ```javascript
    // bad
    foo["bar"];
    foo[myVar];

    // good
    foo.bar;
    ```

*   Because of the difference in naming conventions between ruby and JS, using bracket notation to access `snake_case`
    properties in JSON responses from AJAX requests is preferred.

    _(Ideally, the JSON response is being serialized on server such that attribute names are in `camelCase`, but
    `ActiveModel::Serializers` is usually too inefficient for ad hoc endpoints due to "embedding".)_

    _(TODO: write our own Rails object serializer)_

    ```javascript
    json = $.parseJSON(response.responseText);

    // bad
    json.image_id;

    // good
    json["image_id"];

    // best
    json.imageId;
    ```

*   Use dot notation when calling functions or traversing namespaces.

    ```javascript
    // bad
    Ascent["History"].pushState();

    // good
    Ascent.History.pushState();
    ```

## jQuery

### Attaching jQuery Events

*   Attach the event as close to element in the DOM as possible.

*   Avoid attaching events to `document` whenever possible.

*   If the element doesn’t yet exist on the page, attach it to **closest parent** that does exist on page when the DOM is loaded.

*   `click` (and similar functions) are shorthand for `on("click", ...)`
    *   When a [shorthand function exists](http://api.jquery.com/category/events/), use that over `on`, unless you need
        to call `off`, set a namespace on the event, or listen for events on elements loaded dynamically.

*   Attach events using the `on` function _only_ if the element does not exist in the DOM after initial page load.
    This is useful when elements added to the page via AJAX or dynamic creation need to be observed.

#### An example attaching jQuery events

Given the following HTML:
```html
<div class="foo">
  <a href="#" class="link_to_submit">Submit</a>

  <div class="bar">
    <!--- content loaded via AJAX call
      <a class="loaded_via_ajax">Do something</a>
    -->
  </div>
</div>
```

```javascript
  // bad - event incorrectly attached to document & using `on` to attach event
  $(document).on("click", ".link_to_submit", function() {});

  // good
  $(".link_to_submit").click(function() {});

  // ----------------------------------------------------------------------------

  // bad - event attached to element that does not exist in the DOM at the time of page load
  $(".loaded_via_ajax").click(function() {});

  // bad - event attached to document instead of closest parent
  $(document).on("click", ".loaded_via_ajax", function() {});

  // good - event attached to closest parent that existed in the DOM after initial page load
  $(".bar").on("click", ".loaded_via_ajax", function() {});
```

### jQuery Sugar

jQuery provides a number of [useful utilities](http://api.jquery.com/category/utilities/).
Use these in favor of vanilla javascript where applicable.

*   Prefer `$.each` over `for()`.

*   Use `$.extend` to merge objects in a similar way to ruby's `Hash#merge`.

*   Use `$.inArray` to test for objects in an array similarly to ruby's `include?`.

## Common Patterns

### Page Initialization Pattern

*   Avoid inline Javascript in your views using `<script>` tags.

    *   An acceptable use-case for a `<script>` tag in the view is to include a JSON string of serialized data for use
        on the page. This is common with React and serialized objects.

*   Prefer creating page-specific initialization files.

*   Use the following naming convention for page initialization files:

    `controller_name.action_name.js`

*   Avoid using controller-wide initialization files. Prefer using individual files for each action as necessary.

*   The `<body>` tag has `data-controller` and `data-action` attributes to help target specific pages.

*   When creating a page initialization, use the pattern:

    ```javascript
    (function() {
      var initialize = function(options) {};

      var anotherFunction = function() {};

      $(function() {
        if ($("body[data-controller=foo][data-action=bar]").length) {
          initialize();
        }
      });
    });
    ```

### Module Definition Pattern

*   Any Ascent-specific module should be namespaced to `Ascent`.

    ```javascript
    Ascent.History = (function() {});
    ```

*   When creating a new module, use the module pattern:

    ```javascript
    Ascent.MyAwesomeModule = (function() {
      var exports = {};

      // more private variables

      exports.initialize = function(init_vars) {};

      exports.publicFunction = function() {};

      // more public functions

      // private

      function privateFunction() {};

      // more private function declarations

      return exports;
    }());
    ```

*   In most cases, your module should define an `initialize` function to prepare the module for use on the page.

*   Do not auto initialize modules within the module js file
    (ex: adding `$(Ascent.MyAwesomeModule.initialize)` to the end of the file).

    Instead, include the module initialization in the appropriate page initialization file. 

### Class Definition Pattern

*   Any Ascent-specific class should be namespaced to `Ascent`.

*   When creating a javascript class that you need to create multiple instances of, use the following pattern:

    ```javascript
    // Constructor
    Ascent.MyObject = function(arg1, arg2) {
      // Set/define public attributes
      this.arg1 = arg1;
      this.arg2 = arg2;

      // Initialize object/DOM state

      // Private constructor function declarations
      function privateConstructorFunction() {};
    };

    // Instance functions
    (function() {
      // Public instance functions
      this.getArg1 = function() {
        return this.arg1;
      };

      this.getArg2 = function() {
        return this.arg2;
      };

      this.setArg1 = function(newArg1) {
        this.arg1 = newArg1;

        return this;
      };

      this.modifyDOM = function(callback) {
        // Do something

        callback(this);

        return this;
      };

      // Private instance function declarations
      function privateInstanceFunction() {};
    }).call(Ascent.MyObject.prototype);
    ```

    Instatiate new objects:

    ```javascript
    var object1 = new Ascent.MyObject("foo", "bar");

    var object2 = new Ascent.MyObject("oof", "rab");
    ```

*   In instance functions that modify the object, it is useful to return the object itself in order to chain calls.

*   In instance functions that accept callbacks, it is usually most useful to yield the object to the callback function.

### jQuery Plugin Pattern

*   When writing a jQuery extension, use the following pattern:

    ```javascript
    (function($) {
      var methods = {
        init: function(options) {
          var defaults = {
            option1: "foo"
          };

          var settings = $.extend({}, defaults, options);

          // DO NOT define private functions within the `each` loop
          var privateFunction = function() {};

          return this.each(function() {
            var el = $(this);
            var myObject = new Ascent.MyObject();

            el.data("my-object", myObject);
          });
        },

        doSomething: function() {
          // do something to each matching element, probably working on data stored on the object
          this.each(function() {  
            var el = $(this);
            var myObject = el.data("my-object");

            // ...
          });
        }
      };

      $.fn.myPlugin = function(methodOrOptions) {
        if (methods[methodOrOptions]) {
          return methods[methodOrOptions].apply(this, Array.prototype.slice.call(arguments, 1));

        } else if (typeof methodOrOptions === 'object' || !methodOrOptions) {
          return methods.init.apply(this, methodOrOptions);

        } else {
          $.error("Undefined method `"+  methodOrOptions + "` for $.selectMenu.");
        }
      };
    }(jQuery));
    ```

    Example usage:

    ```javascript
    $("div").myPlugin({ option1: "bar" });
    $("div").myPlugin("doSomething");
    ```

## References

*   [JS on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
