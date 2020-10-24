---
layout: post
title: Higher order functions and where to find them - Decorators
comments: true
tags: [python, decorators, functional]
---
We saw [what higher order functions are](/2020/10/21/higher-order-functions-1) and how they can be used in [creating partially applied functions](/2020/10/22/higher-order-functions-2) in previous posts. Now let us see how we can use higher order functions to implement python decorators.

Let us say you want to intercept your function call to log the arguments passed and the return value. ie.
```python
def add(a, b):
    return a + b

add(2, 3)
# When called with 2 and 3 as arguments, it should print
> Function add called with arguments: 2, 3
> Function add returned: 5
```
It is quite easy to do this. Let us start with defining a function `log`.
```python
def log(fun):
    def inner(*args, **kwargs):
        print(
            f"Function {fun.__name__} "
            f"called with arguments: {*args, *kwargs}")
        ret_value = fun(*args, **kwargs)
        print(f"Function {fun.__name__} returned: {ret_value}")
        return ret_value
    return inner

add_with_logging = log(add)
add_with_logging(1, 2)

# Prints the following
> Function add called with arguments: (1, 2)
> Function add returned: 3
```
But this isn't good. You are creating a new function and have to replace all usages of `add` with `add_with_logging`. There is an easy fix.
```python
# Redefine the name add to log(add)
add = log(add)
add(1, 2)
# Prints the following
> Function add called with arguments: (1, 2)
> Function add returned: 3
```
This is called a **decorator pattern** and is quite widely used in python. Python provides some [syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar) for this usage. All you need to do is add `@log` before the function definition.
```python
@log
def add(a, b):
    return a + b
add(1, 2)
# Prints the following
> Function add called with arguments: (1, 2)
> Function add returned: 3
```
