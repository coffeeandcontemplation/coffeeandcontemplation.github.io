---
layout: post
title: Higher order functions and where to find them
comments: true
tags: [python, functional]
---
My first encounter with a higher order function was when I ran `man qsort` and noticed its type.

```c
void qsort(void *base, size_t nmemb, size_t size,
            int (*compar)(const void *, const void *));
```

For someone who had only seen primitive types like `int` and `long` and `char`, `int (*compar)(const void *, const void *)` was quite the surprise.
I later found out that it was the type for a function which took two arguments and returned an `int`. Complicated looking syntax aside, this is quite interesting. Passing a function allows you to create a generic `qsort` function which can be use to sort anything with a custom sorting logic.

> A higher order function is a function that takes other functions as arguments or return functions.

When I picked up python a few years later, higher order functions were everywhere. And pretty easy to use.

```python
# Let us define a function that prints "Hello world"
def hello():
    print("Hello world")

# call is a higher order function which just calls a function passed to it without arguments
def call(fun):
    fun()

# Look ma! I passed a function
call(hello)  # prints Hello world
```

Now, what if you want to make a function which can call functions which takes arguments. This is were python's `*args` and `**kwargs` come into picture. Let us redefine our `call` function.

```python
# add takes two numbers and return the sum
def add(a, b):
    return a+b

def call(fun, *args, **kwargs):
    return fun(*args, **kwargs)

# Call add with positional arguments
call(add, 1, 2)  # returns 3

# Call add with named arguments
call(add, a=1, b=2)  # returns 3
```

Functions in python can return other functions too

```python
def make_hello():
    def say_hello():
        print("Hello world")
    return say_hello

# Call make_hello and assign the return function to a variable.
# Then use it like any normal function
hello = make_hello()
hello()
```
Now, let us do something fun with this

```python
def make_adder(n):
    def adder(m):
        return m + n
    return adder

add_one = make_adder(1)
add_one(10) # returns 11

add_42 = make_adder(42)
add_42(10) # returns 52
```
Here, we return a function `adder` from inside `make_adder`. Notice how the inner function has access to the variable `n` even after the execution of `make_adder` is complete. This inner function along with its scope is called a __closure__.

Languages like `python`, `javascript`, `dart` etc supports closures.
