# Python

Much of this style guide directly follows [PEP 8](http://pep8.org/). For cases that are not covered here, we encourage you to follow those guidelines. Additionally, [PEP 20](https://www.python.org/dev/peps/pep-0020/) contains some helpful pointers as well.

## General Coding Style

* 4 spaces per indentation level
    * PyCharm: Preferences -> Code Style -> Python -> Tabs and Spaces
    * Vim: `set tabstop=4` in `.vimrc`
* (Roughly) 120 characters per line
    * Depending on the context, this can be broken! It's preferable to have a longer line that defines or uses explictly named variables or methods than to religiously follow a specific line length
    * That said, shorter lines make it easier to compare two files, particularly during code review. 
    * In general, insert line breaks in between parentheses, brackets, and braces to divide longer statements
    * However, using a backslash to divide lines is acceptable where there is no other option (such as within a context manager or `assert` statement)
    
```python
# In general, insert line breaks to divide functions
def some_very_long_function_with_lots_of_arguments(argument1,
                                                   argument2,
                                                   argument3):
    pass
    
# But using a \ is also allowed in some cases such as context managers

with open('this/directory/is/very/long/youknow.txt', 'rb') as fileone, \
    open('this/other/directory/is/also/really/long/youknow.txt', 'rb') as filetwo:
    
    pass
```

* 2 lines between top level functions and classes, 1 line between methods within a class
* While single and double quotes to denote strings are both acceptable, keep their use consistent across the project
* Multiple statements on the same line are discouraged (avoid `if x: y()`), though, in isolated cases it can be ok. In general, however, it is strongly encouraged to put statements on new lines.

## Imports

* Group imports in the following order, separated by a blank line (alphabetical within each grouping):
    * Standard Library (`re`, `math`)
    * Third Party Imports (`pandas`, `scikit-learn`)
    * Local Application Imports (`parsley.models`, etc.)
* Absolute imports are highly recommended (`from sklearn.linear_models import LogisticRegression`)
* However, if required, explicit relative imports are allowed (`from .models import ReferenceDocument`)
* Imports in Python generally can be tricky. [This](https://stanford.edu/~chrisyeh/2017/08/08/definitive-guide-python-imports.html) is a good resource to help debug any ongoing import challenges. 
* We import some libraries in a specific way due to convention (preferred, but can be broken for a good reason):
    * Import `pandas` and `numpy` as `pd` and `np` respectively (`import pandas as pd`)
    * Import individual objects from their modules in `scikit-learn` and `keras` (`from sklearn.linear_models import LogisticRegression`, `from keras.layers import Dense`)
    * When applicable but not detailed here, try to import third-party libraries in the same style that their docs do (such as `nlp = spacy.load('en')` or `import matplotlib.pyplot as plt`)
* 

## Whitespace

A lot of whitespace issues come down to a need for standardization versus any specific benefit to one way of doing something. What follows are standards laid out by PEP8, which we follow more or less verbatim in this area. Picking a standard of any kind reduces friction in reading new code and cuts down on noise when comparing differences between code. Keeping the same standards as the broader Python community (such as with PEP8) has an additional benefit of reducing the amount of time that new developers require to get up to speed with our code. In general, we try to stick to the following standards: 

* **One space before and after assignment (`=`) or any other operator (`a = b, a + b, a - b, a % b`, etc.)**
    * However, no spaces surrounding `=` for a keyword argument (`a(param=b)` not `a(param = b)`), as we are not assigning or modifying a variable for keyword arguments
    * We recommend against using extra spaces to line up assignments, as that may make code more difficult to read and require more work to keep things lined up. 

```python
# Recommended 

a = 2
long_variable_name = function(parameter)

# Not Recommended

a                  = 2
long_variable_name = 2
```

* **Do not add extra whitespace in these cases** as it can lead to a lot of visual noise when comparing commits or be difficult to read when inconsistent:
    * Inside parentheses or brackets (`a = (b[0], b[1])`)
    * After a trailing comma before a closing parenthesis (`c = (0,)`)
    * Immediately before a comma, semicolon, or colon (`if x == 4: print x, y; x, y = y, x`) 
    * Immediately before an opening parenthesis that starts the argument list of a function call (`foo(a)` not `foo (a)`)
    * Immediately before any slices or indices (`foo[1:5]` not `foo [1:5]`)
    
## Naming

* Variables should use snake case and be expressive (`variable_one`)
    * Avoid one-letter variables unless used in-line:
```python
# Recommended 
ten_item_list = [x for x in range(10)]

# Not Recommended 
r = pd.read_csv('example.csv')
```
* However, certain established shorthand variables are acceptable, particularly if they are used in the library's documentation as well (such as `df` for DataFrame or `r` for a Requests object).
* Functions should use lowercase snake case:

```python
# Recommended 
def my_new_function():
    pass

# Not Recommended 
def newFunction():
    pass
```
* Classes should be named in Capital Case (`NewClass`)

* Class have three types of methods that can be assigned (see [here](https://realpython.com/instance-class-and-static-methods-demystified/) or [this stackexchange thread](https://softwareengineering.stackexchange.com/questions/306092/what-are-class-methods-and-instance-methods-in-python) for a primer as well):
    * _Instance methods_ are used in most cases. They have access to `self`, which refers to the instantiated class object, and can freely access or use attributes and other methods in that instance. They can also modify the instantiated object's state. Given that in most cases, we define a class to refer to or modify some _instance_'s state, instance methods are most often a good fit. 
        * In **most cases**, this is a good fit
    * _Class methods_ use a `@classmethod` decorator. They have access to a `cls` parameter and can access or modify state that could apply to all instances of the class. It inherently knows its own class, but does not refer specifically to any instance of that class. You might typically use this as a factory to generate certain types of classes (this is a frequent pattern in Django and Flask, for example)
    * _Static methods_ use a `@staticmethod` decorator. They have no way to access either the state of the instantiated object or the class, but essentially act as ways to restrict how data is accessed or modified. You can typically imagine this as an attached function that doesn't care about what it is attached to. 
    * Another way to think about the differences across each is to look at what is returned when those methods are called:
        * `A.static_method` behaves as an independent function no matter whether or not the class has been instantiated when it was called. It can be called successfully no matter what the state of the 
        * `A.class_method` inherently returns a reference to the class that it is defined in no matter whether or not the class has been instantiated when it was called. This method will call successfully whether or not the instance is instantiated (but won't have access to any `self` methods or attributes)
        * `A.instance_method` behaves differently. Without instantiating the class, it looks like an independent function, but when the class is instantiated, it refers to a specific object that exists. If the class _has not been instantiated_ the method call will fail (because it has no `self` to refer to)
```python
class A:
    def instance_method(self):
        print('Instance method!')

    @classmethod
    def class_method(cls):
        print('Class method!')

    @staticmethod
    def static_method():
        print('Static method!')

A.instance_method
>>>> <function __main__.A.instance_method>

TypeError                                 Traceback (most recent call last)
<ipython-input-27-ef200e0648d7> in <module>()
----> 1 A.instance_method()

TypeError: instance_method() missing 1 required positional argument: 'self'


A().instance_method
<bound method A.instance_method of <__main__.A object at 0x10c838eb8>>
>>> Instance method!

A.class_method
>>> <bound method A.class_method of <class '__main__.A'>>
A.class_method()
>>> Class method!

A().class_method
>>> <bound method A.class_method of <class '__main__.A'>>
A().class_method()
>>> Class method!

A.static_method
>>> <function __main__.A.static_method>
A.static_method()
>>> Static method!

A().static_method
>>> <function __main__.A.static_method>
A().static_method()
>>> Static method!
```

* Methods / Attributes should typically follow standards for functions, however:
    * If a method name would clash with an established method, add a trailing underscore (i.e., `class_` instead of `clss`)
    * We recommend starting all non-public methods with an underscore (`_private_method()`) for the following reasons:
        * Python lacks a true concept of a private versus a public method
        * However, we do often write methods that are not intended for use except within the class.  
        * Python will not import methods that start with an underscore when all methods are imported through wildcards (such as through `from MyClass import *`)
    * Starting the name with two underscores `__` will invoke name mangling to avoid clashes with any subclasses:
        * For class `MyClass` and attribute `__foo`, `__foo` will only be invoked as `_MyClass__foo`
    * Dunder methods (i.e., double underscores starting and ending a value such as `__name_of_method__`) are magic methods such as `__init__` or `__add__`. They should not be invented, only modified as necessary.
* Constants should be in ALL CAPS and separated by underscores and defined at the very top of the module if necessary (`EPOCHS = 250`)


## Strings

* Whenever possible, use string methods (`'abc'.split()`) as opposed to using the string module (`str.split('String to split')`)
    * Because these [methods](https://docs.python.org/3/library/stdtypes.html#string-methods) inherently exist in all strings, calling the library separately isn't needed
* For string interpolation, we highly prefer the [`.format()`](https://docs.python.org/3/library/string.html#formatstrings) syntax (`'{}'.format(x)`) over the `%` style (`'%' % x`) or joining strings using `+`. The `%` style has been depreciated (though it is still valid Python) and `format` offers a number of benefits, including visual clarity and being able to assign interpolated values to temporary variables:

```python
# Recommended

'I am {name}'.format(name=name)
'We have a {0:2f} degree certainty'.format(x)

# Not Recommended

'I am %' % name
'We have a' + str(round(x)) + 'degree certainty' 

```
* Use string methods such as `startswith()` and `endswith()` instead of rolling out a custom comparison
    * This is clearer to read but also can prevent off-by-one errors

```python
# Recommended
if 'pathing'.startswith('path'):

# Not recommended
if 'pathing'[:3] == 'path':
```
     
## Comments

* In the ideal world, comments would be used sparingly and should ideally answer _why_ a piece of code exists, not _how_ it operates and never _what_ it does
* However, there are a number of cases where you should break this rule, including, but not limited to:
    * External integrations that must be structured in a certain way
    * When there are known risks or places where mistakes are frequently made
    * When code is written in an unexpected way, comments can provide necessary understanding
* Keep comments up-to-date with the code to which they refer. Comments that are misleading are worse than no comments!
* Comments over multiple lines should be at the same indentation level of the code to which they refer
* Inline comments should be rare and follow the guidelines above

## Exceptions

* In general, `try/except` should be used when we have reason to believe that a code block could fail *and* we want to make sure that failure does not disrupt a running program
* When creating new exceptions, always derive from `Exception`, not `BaseException`
* Design exceptions based on what the code that will execute after the exception is raised will need, not based on where the exception occurred. In other words, design exceptions to answer "What went wrong?" versus "A problem occured"
* When capturing exceptions, use specific exceptions instead of a bare `except:`. The bare `except:` clause will capture `SystemExit` and `KeyboardInterruption` exceptions as well, which can make it harder to kill a running program with `Ctrl-C` and can hide exceptions that we care about. 
* Limit the amount of code within any given `try` statement so that you can more easily find where any bugs may hide (i.e., put only the code that you think is prone to raise an exception within the `try` statement)
* Be wary about using `pass` or `continue` within an `except` statement: this can have the unintended effect of hiding unexpected exceptions. 
* When raising errors to an error tracker like Rollbar or logging, please pass specific details on the exception upwards (rather than using a generic message like "Read error"). This can help other developers debug behavior more quickly.

```python

try:
    function_that_may_raise_an_exception()
# Recommended
except Exception as e:
    logging.error('File read error: raised {} exception'.format(e))
# Not Recommended
except Exception as e:
    logging.error('File read error: could not read file')
```

* If you are catching two exceptions in one line, capture them as a group or you will cause a different error

```python
# Recommended
try:
    something_that_will_raise_exception()
except (TypeError, ValueError) as e:
    do_something(e)
    
# Not Recommended
try:
    something_that_will_raise_exception()
except TypeError, ValueError as e:
    do_something(e) # will raise IndexError, list out of range
```
* In Python 3, you can only access `e` within the `except` block. In the following snippet, Python 3 will not be able to evaluate `print(e)`, as it is no longer defined once outside of the `except:` block.

```python
# Recommended
try:
    pass
except Exception as e:
    do_something(e)

# Not Recommended
try:
    pass
except Exception as e:
    do_something()
print(e)
```
* `else` and `finally` should be used when applicable (see [here](https://docs.python.org/3/tutorial/errors.html) for more details and examples):
    * `else` will execute if the code _does not raise an exception_. This can be helpful if there is code that must be completed once the code in your `try` statement finishes successfully 
    * `finally` will execute once the code operation moves out of the `try` clause, regardless of whether or not an exception was raised. This can be useful if an open connection to a third-party service or database needs to be closed, for example. 

## General Development Guidelines

* Try not to compare truthy or falsy objects to `True` or `False`. Because they will already evaluate to `True` or `False`, the added comparison will be overly verbose

```python
# Recommended
if thing_that_is_true:

# Not Recommended
if thing_that_is_true == True:

# Not Recommended
if thing_that_is_true is True:
```
* To check if a collection is empty, make use of the fact that an empty collection evaluates to `False`:
```python
# Recommended
if my_list:

# Not Recommended
if len(my_list) > 0:
```
* Use `is` or `is not` when comparing a variable to a singleton like `None`. These objects can be compared directly within a statement versus having to be tested for equality 

```python
# Recommended
if x is None:
    pass
    
# Not Recommended
if x == None:
    pass
```

* Use `is not` over `if not ... is`
* Always use `def` instead of `lambda` if you will bind that expression to an identifier. This improves readability of tracebacks. Additionally, assigning a lambda expression to an identifier removes the key benefit of using such an expression (that its definition and use can be embedded in a larger expression).
```python
# Recommended
def add_two(val):
    return val + 2
    
# Recommended
add_two = lambda val: return val + 2
```

* Using a context manager (`with`) when acquiring or releasing materials is preferred, as this will ensure that the materials you've requested in Python are automatically released (and not left in an uncontrolled or zombie state)

```python
# Recommended
with open('my_file.txt', 'rb') as infile:
    print(infile.read())
    
# Not Recommended
infile = open('my_file.txt', 'rb')
print(infile.read())

```
* When using a context manager (`with`), make sure to invoke specific methods whenever doing anything more than acquiring or releasing material to explicit about what the resources are using:

```python
# Recommended
with conn.begin_transaction():
    do_stuff_in_transaction(conn)
    
# Not Recommended
with conn:
    do_stuff_in_transaction(conn)
```

* Be consistent in `return` statements
    * Rule of thumb: either a function should _always_ return something (even `None`) or it should _never_ return anything
    * Any cases where a function could return nothing should explicitly return `None`
    
```python 
# Recommended

def under_10(value):
    if value < 10:
        return True
    else:
        return False
        
# Not Recommended
def under_10(value):
    if value < 10:
        return True
        
# Not Recommended
def under_10(value):
    if value < 10:
        return True
    else:
        return
```

* Avoid side effects when possible when writing functions. Ideally, a function should take in a set of parameters and return back some set of responses and not change anything else along the way. Modifying objects out of scope of the function that the function does not return can lead to unintended behavior on the part of the function

```python

number = 2

# Recommended 

def add_two(value):
    return value + 2

number = add_two(number)
>>> 4

# Not Recommended 

def add_two():
    number + 2

add_two()
>>> # nothing returned

# At this point, it is unclear without carefully reading the code to know what value number has
```

## Typical Python Mistakes

* **Using a mutable value as the default in a keyword argument**
    * Python evaluates the default value for a keyword argument only once (when the function is defined) and not when the function is invoked. This can lead to unexpected behavior:
    * Avoid this by explicitly creating a new object whenever the function is invoked (versus when it is defined)
```python
def add_to_list(thing_to_append, value=[]):
    return value.append(thing_to_append)
    
# expected
>>> add_to_list(2) # [2]
>>> add_to_list('foo') # ['foo']

# actual
>>> add_to_list(2) # [2]
>>> add_to_list('foo') # [2, 'foo']

# to avoid
def add_to_list(thing_to_append, value=None):
    if value is None:
        value = []
    return value.append(thing_to_append)
```

* **Assigning attributes with inherited classes can create unexpected behavior.** When a class inherits from another class, it does not create new attributes but refers back to the inherited classes attributes. This means that changing the attributes within the base class may also change the attributes of the inheriting class as well. To avoid:
    * For attributes that should differ based on instantiation, define those attributes as `self.attribute` within a `__init__(self):`
    * For attributes that should remain constant across different instantiations, either define the inherited attribute explicitly (`B.x = 1`) or be aware of how this operates in practice.

```python
class A:
    x = 1
    
class B(A):
    pass
    
# expected
>>> A.x = 2
>>> A.x # 2
>>> B.x # 1

# actual
>>> A.x = 2
>>> A.x # 2
>>> B.x # 2
```

* **Python automatically considers a variable that has had something assigned to it to be local to the scope _within that function_.** This can also lead to unexpected behavior. What happens in the code below is that `new_list += value` is syntactic sugar for `new_list = new_list + value`. Since we are assigning something to a variab le within the function, Python automatically assumes it is within the scope of that function (but, since we haven't yet defined it, Python raises the `UnboundLocalError` exception to let us know that we haven't defined `new_list` yet). To avoid this, either pass in the object as an argument (the preferred option), or use modify the object without assignment (such as through the use of built-in methods). More information can be found [here](https://docs.python.org/3.7/faq/programming.html#why-am-i-getting-an-unboundlocalerror-when-the-variable-has-a-value) in the Python docs.

```python
new_list = [1, 2, 3]

def append_list(value):
    new_list += [value]
    return new_list
    
# expected
>>> append_list(4) # [1, 2, 3, 4]

# actual
>>> append_list(4) #UnboundLocalError
```

* **In general, removing (or modifying) items of a list while iterating over it is not recommended.** It is much better to use list comprehension (`[x for x in range(10) if x % 2 == 0]` versus iterating through the list and removing items) or create a new list and add items to that.
* **When naming modules, be careful that you do not clash with any name in the standard library.** Python may import your module versus the standard library module and, in doing so, cause any number of problems.

# Other Links

- [Hitchhiker's Guide to Python](https://docs.python-guide.org/)