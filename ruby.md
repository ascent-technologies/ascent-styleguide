# Ruby

*   [Coding Style](#coding-style)
*   [Syntax](#syntax)
*   [Naming](#naming)
*   [Classes](#classes)
*   [Exceptions](#exceptions)
*   [Collections](#collections)
*   [Strings](#strings)
*   [Regular Expressions](#regular-expressions)
*   [Percent Literals](#percent-literals)
*   [Hashes](#hashes)
*   [Keyword Arguments](#keyword-arguments)

Much of this was taken from [https://github.com/bbatsov/ruby-style-guide](https://github.com/bbatsov/ruby-style-guide).

## Coding Style

*   Use soft-tabs with a two space indent.

*   Try to keep lines under 120 characters.

*   Avoid trailing whitespace.

*   End each file with a newline.

*   Use spaces around operators, after commas, colons and semicolons, after `{` and before `}`, and arround array initialization.

    ```ruby
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts "Hi"
    [ 1, 2, 3 ].each { |e| puts e }
    "some sort of #{ some_variable } interpolation"
    ```

*   No spaces after `(` or before `)`, or inside `#[]` method calls.

    ```ruby
    some(arg).other
    something[1]
    something[1, 2]
    ```

*   No spaces after `!`.

    ```ruby
    !array.include?(element)
    ```

*   Indent `when` relative to line containing `case`.

    ```ruby
    # bad (annoying to maintain)
    kind = case year
           when 1850..1889
             "Blues"

           when 1890..1909
             "Ragtime"

           when 1910..1929
             "New Orleans Jazz"

           when 1930..1939
             "Swing"

           when 1940..1950
             "Bebop"

           else
             "Jazz"
           end

    # good
    kind = case year
    when 1850..1889
      "Blues"

    when 1890..1909
      "Ragtime"

    when 1910..1929
      "New Orleans Jazz"

    when 1930..1939
      "Swing"

    when 1940..1950
      "Bebop"

    else
      "Jazz"
    end

    # okay
    kind = case year
      when 1850..1889
        "Blues"

      when 1890..1909
        "Ragtime"

      when 1910..1929
        "New Orleans Jazz"

      when 1930..1939
        "Swing"

      when 1940..1950
        "Bebop"

      else
        "Jazz"
    end
    ```

*   Use empty lines as method separators between `def`s and to break up a method into logical paragraphs.

    ```ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result = if true
        do_something

      elsif false
        do_something_else

      else
        just_do_this
      end
    end
    ```

*   When continuing a chained method invocation on another line, include the . on the first line to indicate that the expression continues.

    ```ruby
    # bad - need to read ahead to the second line to know that the chain continues
    one.two.three
      .four

    # good - it's immediately clear that the expression continues beyond the first line
    one.two.three.
      four
    ```

*   _Do not_ align the parameters of a method call if they span more than one line.

    ```ruby
    # starting point (line is too long)
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
    end

    # bad (double indent)
    def send_mail(source)
      Mailer.deliver(
          to: 'bob@example.com',
          from: 'us@example.com',
          subject: 'Important message',
          body: source.text)
    end

    # bad (parameters aligned; frustrating to maintain)
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com',
                     from: 'us@example.com',
                     subject: 'Important message',
                     body: source.text)
    end

    # good (normal indent)
    def send_mail(source)
      Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text
      )
    end
    ```

## Syntax

*   Use `def` with parentheses when there are arguments. Omit the parentheses when the method doesn't accept any arguments.

    ```ruby
      def some_method
        # body omitted
      end

      def some_method_with_arguments(arg1, arg2)
        # body omitted
      end
     ```

*   Never use `for`, unless you know exactly why. Most of the time iterators should be used instead. `for` is
    implemented in terms of `each` (so you're adding a level of indirection), but with a twist - `for` doesn't introduce
    a new scope (unlike `each`) and variables defined in its block will be visible outside it.

    ```ruby
    arr = [ 1, 2, 3 ]

    # bad
    for elem in arr do
      puts elem
    end

    # good
    arr.each { |elem| puts elem }
    ```

*   Never use `then` for multi-line `if/unless`.

    ```ruby
    # bad
    if some_condition then
      # body omitted
    end

    # good
    if some_condition
      # body omitted
    end
    ```

*   Avoid the ternary operator (`?:`) except in cases where all expressions are extremely trivial. However, do use the
    ternary operator(`?:`) over `if/then/else/end` constructs for single line conditionals.

    ```ruby
    # bad
    result = if some_condition then something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

*   Use one expression per branch in a ternary operator. This also means that ternary operators should not be nested.
    Prefer `if/else` constructs in these cases.

    ```ruby
    # bad
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # good
    if some_condition
      nested_condition ? nested_something : nested_something_else

    else
      something_else
    end
    ```

*   The `and` and `or` keywords are banned for _logic_. Always use `&&` and `||` for logic, and only use `and` and `or` for flow control.

    ```ruby
    # bad
    if one and two
      do_something
    end

    # good
    result = do_something or return

    # okay
    render_error and return false
    ```

*   Avoid multi-line `?:` (the ternary operator), use `if/unless/else` instead.

*   Favor modifier `if/unless` usage when you have a single-line body with a short condition and body.

    ```ruby
    # bad
    if some_condition
      do_something
    end

    # good
    do_something if some_condition

    # another good option
    some_condition && do_something

    # okay
    if (some_condition && some_other_condition) || just_do_it
      do_something_fancy(with_arguments, and_everything)
    end
    ```

*   Favor `unless` over `if` for negative conditions (or control flow `||`).

    ```ruby
    # bad
    do_something if !some_condition

    # good
    do_something unless some_condition

    # another good option
    some_condition || do_something
    ```

*   Do not use `unless` with `else`. Rewrite these with the positive case first.

    ```ruby
    # bad
    unless success?
      puts "failure"

    else
      puts "success"
    end

    # good
    if success?
      puts "success"

    else
      puts "failure"
    end
    ```

*   Don't use parentheses around the condition of an `if/unless/while`.

    ```ruby
    # bad
    if (x > 10)
      # body omitted
    end

    # good
    if x > 10
      # body omitted
    end
    ```

*   Prefer `{...}` over `do...end` for single-line blocks. Avoid using `{...}` for multi-line blocks (multiline chaining
    is always ugly). Always use `do...end` for "control flow" and "method definitions" (e.g. in Rakefiles and certain
    DSLs). Avoid `do...end` when chaining.

    ```ruby
    names = [ "Bozhidar", "Steve", "Sarah" ]

    # good
    names.each { |name| puts name }

    # okay - prefer single line
    names.each do |name|
      puts name
    end

    # good
    names.select { |name| name.start_with?("S") }.map { |name| name.upcase }

    # bad - chained `map` call with `do...end`
    names.select do |name|
      name.start_with?("S")
    end.map { |name| name.upcase }
    ```

*   Avoid `return` where not required.

    ```ruby
    # bad
    def some_method(some_arr)
      return some_arr.size
    end

    # good
    def some_method(some_arr)
      some_arr.size
    end
    ```

*   Use spaces around the `=` operator when assigning default values to method parameters:

    ```ruby
    # bad
    def some_method(arg1=:default, arg2=nil, arg3=[])
      # do something...
    end

    # good
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # do something...
    end
    ```

    While several Ruby books suggest the first style, the second is much more prominent in practice (and arguably a bit more readable).

*   Using the return value of `=` (an assignment) is ok.

    ```ruby
    # okay
    if (v = array.grep(/foo/)) ...

    # okay (prefer parenthesis)
    if v = array.grep(/foo/) ...

    # also okay - has correct precedence.
    if (v = next_value) == "hello" ...
    ```

*   Use `||=` freely to initialize variables.

    ```ruby
    # set name to Bozhidar, only if it's nil or false
    name ||= "Bozhidar"
    ```

*   Don't use `||=` to initialize boolean variables. (Consider what would happen if the current value happened to be `false`.)

    ```ruby
    # bad - would set enabled to true even if it was false
    enabled ||= true

    # good
    enabled = true if enabled.nil?
    ```

*   Avoid using Perl-style special variables (like `$0-9`, `$`, etc. ). They are quite cryptic and their use in anything
    but one-liner scripts is discouraged. Prefer long form versions such as `$PROGRAM_NAME`.

*   Never put a space between a method name and the opening parenthesis.

    ```ruby
    # bad
    f (3 + 2) + 1

    # good
    f(3 + 2) + 1
    ```

*   If the first argument to a method begins with an open parenthesis, always use parentheses in the method invocation.

    ```ruby
    # bad
    f (3 + 2) + 1

    # good
    f((3 + 2) + 1)
    ```

*   Prefix unused block parameters with `_`.

    ```ruby
    # bad
    result = hash.map { |k, v| v + 1 }

    # good
    result = hash.map { |_k, v| v + 1 }

    # okay (provides documentation for future maintenance)
    result = array.map { |(center, _extents, _color)| center }
    ```

*   Don't use the `===` (threequals) operator to check types. `===` is mostly an implementation detail to support Ruby
    features like `case`, and it's not commutative. For example, `String === "hi"` is true and `"hi" === String` is
    false. Instead, use `is_a?` or `kind_of?` if you must.

    Refactoring is even better. It's worth looking hard at any code that explicitly checks types.

*   Avoid use of nested conditionals for flow of control.

    Prefer a guard clause when you can assert invalid data. A guard clause is a conditional statement at the top of a
    function that bails out as soon as it can.

    ```ruby
    # bad
    def compute_thing(thing)
      if thing[:foo]
        update_with_bar(thing[:foo])

        if thing[:foo][:bar]
          partial_compute(thing)

        else
          re_compute(thing)
        end
      end
    end

    # good
    def compute_thing(thing)
      return unless thing[:foo]
      update_with_bar(thing[:foo])

      return re_compute(thing) unless thing[:foo][:bar]
      partial_compute(thing)
    end
    ```

    Prefer next in loops instead of conditional blocks.

    ```ruby
    # bad
    [ 0, 1, 2, 3 ].each do |item|
      if item > 1
        puts item
      end
    end

    # good
    [ 0, 1, 2, 3 ].each do |item|
      next unless item > 1

      puts item
    end
    ```

## Naming

*   Use `snake_case` for methods and variables.

*   Use `CamelCase` for classes and modules. (Keep acronyms like HTTP, RFC, XML uppercase.)

*   Use `SCREAMING_SNAKE_CASE` for other constants.

*   The names of predicate methods (methods that return a boolean value) should end in a question mark. (i.e. `Array#empty?`).

*   Prefer positive names for booleans. Positive names are much easier to reason about when they're combined with other booleans.
    ```
    # bad
    disabled = true
    
    # good
    enabled = false
    ```

*   The names of potentially "dangerous" methods (i.e. methods that modify `self` or the arguments, `exit!`, etc.)
    should end with an exclamation mark. Bang methods should only exist if a non-bang method exists.
    ([More on this](http://dablog.rubypal.com/2007/8/15/bang-methods-or-danger-will-rubyist)).

## Classes

*   Avoid the usage of class (`@@`) variables due to their unusual behavior in inheritance.

    ```ruby
    class Parent
      @@class_var = "parent"

      def self.print_class_var
        puts @@class_var
      end
    end

    class Child < Parent
      @@class_var = "child"
    end

    Parent.print_class_var
    # => will print "child"
    ```

    As you can see all the classes in a class hierarchy actually share one class variable. Class instance variables
    should usually be preferred over class variables.

*   Avoid the usage of class (`@@`) variables due to their lack of threading support.

    ```ruby
    module Something
      # bad
      def self.do_something
        @@class_var = "hi"
      end

      # good
      def self.do_something_else
        Thread.current[:class_var] = "hi"
      end
    end
    ```

*   Use `def self.method` to define singleton methods. This makes the methods more resistant to refactoring changes.

    ```ruby
    class TestClass
      # bad
      def TestClass.some_method
        # body omitted
      end

      # good
      def self.some_other_method
        # body omitted
      end
    end
    ```

*   Avoid `class << self` except when necessary, e.g. single accessors and aliased attributes.

    ```ruby
    class TestClass
      # bad
      class << self
        def first_method
          # body omitted
        end

        def second_method_etc
          # body omitted
        end
      end

      # good
      class << self
        attr_accessor :per_page
        alias_method :nwo, :find_by_name_with_owner
      end

      def self.first_method
        # body omitted
      end

      def self.second_method_etc
        # body omitted
      end
    end

    # good (using `extend self`)
    module TestModule
      extend self

      def first_method
        # body omitted
      end
    end
    ```

*   Indent the `public`, `protected`, and `private` methods as much the method definitions they apply to. Separate them
    from surrounding code with a blank line.

    ```ruby
    class SomeClass
      def public_method
        # ...
      end

      private

      def private_method
        # ...
      end
    end
    ```

*   Avoid explicit use of `self` as the recipient of internal class or instance messages unless to specify a method shadowed by a variable.

    ```ruby
    class SomeClass
      attr_accessor :message

      def greeting(name)
        message = "Hi #{ name }" # local variable in Ruby, not attribute writer
        self.message = message
      end
    end
    ```

## Exceptions

*   Don't use exceptions for flow of control.

    ```ruby
    # bad
    begin
      n / d
    rescue ZeroDivisionError
      puts "Cannot divide by 0!"
    end

    # good
    if d.zero?
      puts "Cannot divide by 0!"
    else
      n / d
    end
    ```

*   Avoid rescuing the `Exception` class. Isolate your rescue statement to a subset of classes. If that's not possible,
    rescue `StandardError` instead.

    ```ruby
    # bad
    begin
      # an exception occurs here
    rescue Exception
      # exception handling
    end

    # good (a blind rescue rescues from StandardError, not Exception as many programmers assume)
    begin
      # an exception occurs here
    rescue => e
      # exception handling
    end

    # also good
    begin
      # an exception occurs here
    rescue StandardError => e
      # exception handling
    end
    ```

## Collections

*   Prefer `%w` to the literal array syntax when you need an array of strings.

    ```ruby
    # bad
    STATES = [ "draft", "open", "closed" ]

    # good
    STATES = %w[ draft open closed ]
    ```

*   Prefer `%i` to the literal array syntax when you need an array of symbols.

    ```ruby
    # bad
    STATES = [ :draft, :open, :closed ]

    # good
    STATES = %i[ draft open closed ]
    ```

*   Use `Set` instead of `Array` when dealing with unique elements. `Set` implements a collection of unordered values
    with no duplicates. This is a hybrid of `Array`'s intuitive inter-operation facilities and `Hash`'s fast lookup.

*   Use symbols instead of strings as hash keys.

    ```ruby
    # bad
    hash = { "one" => 1, "two" => 2, "three" => 3 }

    # good
    hash = { one: 1, two: 2, three: 3 }
    ```

## Strings

*   Prefer string interpolation instead of string concatenation:

    ```ruby
    # bad
    email_with_name = user.name + " <" + user.email + ">"

    # good
    email_with_name = "#{ user.name } <#{ user.email }>"
    ```

*   With interpolated expressions, there should be padded-spacing inside the braces.

    ```ruby
    # bad
    "From: #{user.first_name}, #{user.last_name}"

    # good
    "From: #{ user.first_name }, #{ user.last_name }"
    ```

*   Use double-quoted strings. Interpolation and escaped characters will always work without a delimiter change,
    and `'` is a lot more common than `"` in string literals. RubyMine's inspection for double quoted strings without
    interpolation can be disabled by unchecking `Preferences -> Editor -> Inspections -> Ruby -> Double quoted string`.

    ```ruby
    # bad
    name = 'Bozhidar'

    # good
    name = "Bozhidar"
    ```

*   Avoid using `String#+` when you need to construct large data chunks. Instead, use `String#<<`. Concatenation mutates
    the string instance in-place and is always faster than `String#+`, which creates a bunch of new string objects.

    ```ruby
    # good and also fast
    html = ""
    html << "<h1>Page title</h1>"

    paragraphs.each do |paragraph|
      html << "<p>#{ paragraph }</p>"
    end
    ```

## Regular Expressions

*   Avoid using $1-9 as it can be hard to track what they contain. Named groups can be used instead.

    ```ruby
    # bad
    /(regexp)/ =~ string
    ...
    process $1

    # good
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

*   Use `String#[]` for simple regex captures.

    ```ruby
    something = "a string of something"[/(some.+)\Z/, 1]
    ```

*   Be careful with `^` and `$` as they match start/end of line, not string endings. If you want to match the whole string use: `\A` and `\z`.

    ```ruby
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\z/] # don't match
    ```

*   Use `x` modifier for complex regexps. This makes them more readable and you can add some useful comments. Just be careful as spaces are ignored.

    ```ruby
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

## Percent Literals

*   Use `%w` freely.

    ```ruby
    STATES = %w[ draft open closed ]
    ```

*   Use `%i` freely.

    ```ruby
    STATES = %i[ draft open closed ]
    ```

*   Use `%()` for single-line strings which require both interpolation and embedded double-quotes. For multi-line strings, prefer heredocs.

    ```ruby
    # bad (no interpolation needed)
    %(<div class="text">Some text</div>)
    # should be "<div class=\"text\">Some text</div>"

    # bad (no double-quotes)
    %(This is #{ quality } style)
    # should be "This is #{ quality } style"

    # bad (multiple lines)
    %(<div>\n<span class="big">#{ exclamation }</span>\n</div>)
    # should be a heredoc.

    # good (requires interpolation, has quotes, single line)
    %(<tr><td class="name">#{ name }</td>)
    ```

*   Use `%r` only for regular expressions matching _more than_ one '/' character.

    ```ruby
    # bad
    %r(\s+)

    # still bad
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # good
    %r(^/blog/2011/(.*)$)
    ```

*   Use the braces that are the most appropriate for the various kinds of percent literals.

    `()` for string literals (`%`, `%q`).

    `[]` for array literals (`%w`, `%i`) as it is aligned with the standard array literals.

    `{}` for regexp literals (`%r`) since parentheses often appear inside regular expressions. That's why a less common
    character with `{` is usually the best delimiter for `%r` literals.

    `()` for all other literals (`%s`, `%x`)

## Hashes

*   Use the JSON style introduced in 1.9 for Hash literals instead of hashrocket syntax.

    ```ruby
    # good
    user = {
      login: "defunkt",
      name: "Chris Wanstrath"
    }

    # bad
    user = {
      :login => "defunkt",
      :name => "Chris Wanstrath"
    }
    ```

*   Don't mix hash styles in a single Hash literal

    ```ruby
    # okay
    user = {
      :login => "defunkt",
      :name => "Chris Wanstrath",
      "followers-count" => 52390235
    }
    ```

## Keyword Arguments

[Keyword arguments](http://magazine.rubyist.net/?Ruby200SpecialEn-kwarg) are recommended but not required when a
method's arguments may otherwise be opaque or non-obvious at the call-site. Additionally, prefer them over the old "Hash
as pseudo-named args" style from pre-2.0 ruby.

*   So instead of this:

    ```ruby
    def remove_member(user, skip_membership_check = false)
      # ...
    end

    # Elsewhere: what does true mean here?
    remove_member(user, true)
    ```

*   Do this, which is much clearer.

    ```ruby
    def remove_member(user, skip_membership_check: false)
      # ...
    end

    # Elsewhere, now with more clarity:
    remove_member user, skip_membership_check: true
    ```

*   Avoid keyword arguments that are likely to be redundant at all call sites:

    ```ruby
    # bad
    def remove_member(user: nil, logger: nil)
      # ...
    end

    # Elsewhere
    remove_member user: user, logger: logger

    # Yet elsewhere
    remove_member user: User.find(1), logger: Logger.new(STDOUT)
    ```

*   A good rule of thumb is to use keyword arguments for values that are passed as constants from call-sites.

    ```ruby
    some_method enabled: true
    some_method key: :blah
    some_method count: 5
    ```
