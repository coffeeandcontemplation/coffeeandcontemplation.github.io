---
layout: post
title: Higher order functions and where to find them - Partial Application
comments: true
---
In the [previous post](/2020/10/21/higher-order-functions-1), we discussed higher order functions. Ones that take functions as arguments and ones that return functions as arguments. Now let us look at some that do both.

In languages like haskell, there is a feature called __Currying__. When a function which takes multiple arguments is called with less than the number of expected arguments, it returns another function with the arguments "partially applied"[^1]. For Eg:-

```haskell
-- Define a function
add a b = a + b
-- Call the function.
-- There are no braces for function calls in haskell
add 1 2  -- Returns 3
-- Below even though add is a 2 argument function,
-- we can pass a single argument and "partially apply" the argument
addTen = add 10
-- addTen is a function
addTen 32   -- Returns 42
```

Now, let us try and get this behavior in python. Mind you, we can't get so nice a syntax with our python implementation. What can we expect?
```python
# define our add function
def add(a, b):
    return a + b
```
To partially apply a single argument, let us define a function that can be used as follows:
```python
addTen = partial(add, 10)
```
`partial` should be a function that returns a closure which has access to the function and the argument passed to it.

```python
def partial(fun, arg1):
    def inner(*args):
        return fun(arg1, *args)
    return inner

addTen = partial(add, 10)
addTen(32)    # Returns 42
```
A better production ready version for `partial` is available in the `functools` library of python. You can access it by `from functools import partial`

Partial can be applied to functions which has more than 2 arguments as well.
```python
def add3(a, b, c):
    return a + b + c
addTen = partial(partial(add3, 4), 6)
addTen(32)  # Returns 42
```
-----


[^1]: There are subtle differences between currying and partial application. They aren't significant for this article.
