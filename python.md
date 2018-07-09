# Python

Much of this style guide directly follows [PEP 8](http://pep8.org/). For cases that are not covered here, we encourage you to follow those guidelines. Additionally, [PEP 20](https://www.python.org/dev/peps/pep-0020/) contains some helpful pointers as well.

## General Coding Style

* 4 spaces per indentation level
    * PyCharm: Preferences -> Code Style -> Python -> Tabs and Spaces
    * Vim: `set tabstop=4` in `.vimrc`
* (Roughly) 80 characters per line
    * Depending on the context, this can be broken
    * In general, insert line breaks in between parentheses, brackets, and braces to divide longer statements
    * However, using a backslash to divide lines is acceptable where there is no other option (such as within a context manager or `assert` statement).
    
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
* Specific libraries sometimes have specific ways to import them:
    * Import `pandas` and `numpy` as `pd` and `np` respectively (`import pandas as pd`)
    * Import individual objects from their modules in `scikit-learn` and `keras` (`from sklearn.linear_models import LogisticRegression`, `from keras.layers import Dense`)
    * When applicable but not detailed here, try to import third-party libraries in the same style that their docs do (such as `nlp = spacy.load('en')` or `import matplotlib.pyplot as plt`)

## Whitespace

* **Add one space before and after assignment (`=`) or any other operator (`a = b`)**
    * However, no spaces surrounding `=` for a keyword argument (`a(param=b)` not `a(param = b)`)
* **Do not add extra whitespace in these cases**:
    * Inside parentheses or brackets (`a = (b[0], b[1])`)
    * After a trailing comma before a closing parenthesis (`c = (0,)`)
    * Immediately before a comma, semicolon, or colon (`if x == 4: print x, y; x, y = y, x`) 
    * Immediately before an opening parenthesis that starts the argument list of a function call (`foo(a)` not `foo (a)`)
    * Immediately before any slices or indices (`foo[1:5]` not `foo [1:5]`)
    
## Naming

* Variables should use snake case and be expressive (`variable_one`)
    * Avoid one-letter variables unless used in-line:
```python
# acceptable
ten_item_list = [x for x in range(10)]

# not acceptable
r = pd.read_csv('example.csv')
```
* However, certain established shorthand variables are acceptable, particularly if they are used in the library's documentation as well, such as `df` for DataFrame or `r` for a Requests object.
* Functions should use lowercase snake case:

```python
# good
def my_new_function():
    pass

# bad
def newFunction():
    pass
```
* Classes should be named in Capital Case (`NewClass`)
* Methods / Attributes
    * Should typically follow standards for functions
    * If a method name would clash with an established method, add a trailing underscore (i.e., `class_` instead of `clss`)
    * If a method is designed to be non-public, start it with an underscore (`_private_method()`). 
        * This will prevent the method from being imported through `from MyClass import *`
    * Starting the name with two underscores `__` will invoke name mangling to avoid clashes with any subclasses:
        * For class `MyClass` and attribute `__foo`, `__foo` will only be invoked as `_MyClass__foo`
    * Dunder methods (i.e., double underscores starting and ending a value such as `__name_of_method__`) are magic methods such as `__init__` or `__add__`. They should not be invented, only modified as necessary.
* Constants should be in ALL CAPS and separated by underscores and defined at the very top of the module if necessary (`EPOCHS = 250`)


## Strings

* Whenever possible, use string methods (`'abc'.split()`) as opposed to using the string module (`str.split('String to split')`)
* Use `startswith()` and `endswith()` instead of slicing a string and making a comparison
```python
# good
if 'pathing'.startswith('path'):

# not good
if 'pathing'[:3] == 'path':
```
     
## Comments

* Comments should be used sparingly and should ideally answer _why_ a piece of code exists, not _how_ it operates and never _what_ it does
    * The code itself should tell the reader what it does. If the is too complex to do that, it should be refactored
* Keep comments up-to-date with the code to which they refer. Comments that are misleading are worse than no comments!
* Comments over multiple lines should be at the same indentation level of the code to which they refer
* Inline comments should be rare

## Exceptions

* In general, use exceptions sparingly, as they may hide errors that are important. 
* When creating new exceptions, always derive from `Exception`, not `BaseException`
* Design exceptions based on what the code that will execute after the exception is raised will need, not based on where the exception occurred. In other words, design exceptions to answer "What went wrong?" versus "A problem occured"
* When capturing exceptions, use specific exceptions instead of a bare `except:`. The bare `except:` clause will capture `SystemExit` and `KeyboardInterruption` exceptions as well, which can make it harder to kill a running program with `Ctrl-C` and can hide exceptions that we care about. 
* Limit the amount of code within any given `try` statement so that you avoid hiding bugs 
* If you are catching two exceptions in one line, capture them as a group or you will cause a different error

```python
# good
try:
    something_that_will_raise_exception()
except (TypeError, ValueError) as e:
    do_something(e)
    
# not good
try:
    something_that_will_raise_exception()
except TypeError, ValueError as e:
    do_something(e) # will raise IndexError, list out of range
```
* In Python 3, you can only access `e` within the `except` block. In the following snippet, Python 3 will not be able to evaluate `print(e)`, as it is no longer defined once outside of the `except:` block.

```python
# good
try:
    pass
except Exception as e:
    do_something(e)

# not good
try:
    pass
except Exception as e:
    do_something()
print(e)
```

## General Development Guidelines

* Use `is` or `is not` when comparing a variable to a singleton like `None`

```python
# good
if x is None:
    pass
    
# not good
if x is == None:
    pass
```

* Use `is not` over `if not ... is`
* Always use `def` instead of `lambda` if you will bind that expression to an identifier. This improves readability of tracebacks. Additionally, assigning a lambda expression to an identifier removes the key benefit of using such an expression (that its definition and use can be embedded in a larger expression).
```python
# good
def add_two(val):
    return val + 2
    
# not good
add_two = lambda val: return val + 2
```

* Using a context manager (`with`) when acquiring or releasing materials is preferred

```python
# good
with open('my_file.txt', 'rb') as infile:
    print(infile.read())
    
# not good
infile = open('my_file.txt', 'rb')
print(infile.read())

```
* When using a context manager (`with`), make sure to invoke specific methods whenever doing anything more than acquiring or releasing material to explicit about what the resources are using:

```python
# good
with conn.begin_transaction():
    do_stuff_in_transaction(conn)
    
# not good
with conn:
    do_stuff_in_transaction(conn)
```

* Be consistent in `return` statements
    * Rule of thumb: either a function should _always_ return something (even `None`) or it should _never_ return anything
    * Any cases where a function could return nothing should explicitly return `None`
    
```python 
# good

def under_10(value):
    if value < 10:
        return True
    else:
        return None
        
# not good
def under_10(value):
    if value < 10:
        return True
        
# not good
def under_10(value):
    if value < 10:
        return True
    else:
        return
```
* To check if a collection is empty, make use of the fact that an empty collection evaluates to `False`:
```python
# good
if my_list:

# not good
if len(my_list) > 0:
```
* Avoid comparing `True` or `False` evaluations using `==`:
```python
# good
if thing_that_is_true:

# not good
if thing_that_is_true == True:

# not good
if thing_that_is_true is True:
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
    
class B:
    x = 1
    
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

* In general, removing (or modifying) items of a list while iterating over it is not recommended. It is much better to use list comprehension (`[x for x in range(10) if x % 2 == 0]` versus iterating through the list and removing items) or create a new list and add items to that.
* When naming modules, be careful that you do not clash with any name in the standard library. Python may import your module versus the standard library module and, in doing so, cause any number of problems.

