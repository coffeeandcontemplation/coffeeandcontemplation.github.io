---
layout: post
title: Context managers in python
comments: true
tags: [python, functional]
---
Say, you have to open and process a file in python. You can do that using a function like this
```python
def open_and_process(filename):
    file = open(filename)
    process(file)
    file.close()  # This is important
```
If an exception occurs in the `process` function, `file.close()` won't be executed.

This pattern is the same for other types of resources too:
1. Acquire resource
2. Use resource
3. Release resource

If something fails during the **use** phase, the resource won't be released cleanly.
There are a couple of ways to solve this.
1. Use `try...finally`
```python
def open_and_process(filename):
    try:
        file = open(filename)
        process(file)
    finally:
        file.close()
```
2. Python Context Managers.

You would have seen code in python that looks like this.
```python
with open(filename) as word_file:
    words = word_file.readlines()
```

A **Context Manager** is a class which implements `__enter__` and `__exit__` functions
```python
class context:
    def __init__(self):
        print("initializing")

    def __enter__(self):
        print("entering block")
        return(10)

    def __exit__(self, _type, _value, _trace):
        print("exiting block")


with context() as val:
    print("block")
    print(val)     # This is the return value of __enter__ function

# Output
> initializing
> entering block
> block
> 10
> exiting block
```
When entering the block, the `__enter__` method is called. When exiting the block, the `__exit__` method is called. The cleanup(like closing the file) can be done in the `__exit__` method.

Luckily, python provides a [contextlib](https://docs.python.org/3/library/contextlib.html) class so that you won't have to write a class with `__enter__` and `__exit__` methods.

```python
from contextlib import contextmanager

@contextmanager
def context():
    print("entering")
    try:
        yield 10
    finally:
        print("exiting")

with context() as var:
    print(var)  # this is the yielded value
    print("inside block")

# output
> entering
> 10
> inside block
> exiting
```

An interesting usecase for this is in mocking classes in tests.
Here, we are building a calculator which calls an `AdderService` to perform addition. It calls the `AdderService` using an `AdderServiceClient` class.
```python
class AdderServiceClient:
    def add(self, a, b):
        # This is an external API call. Throwing an exception to
        # indicate failure when called during tests
        raise Exception("This calls an external service "
                        "and should not be used in tests")

class Calculator():
    def __init__(self, adder):
        self.adder = adder

    def add(self, a, b):
        return self.adder.add(a, b)

def test_calculator_adds():
    calc = Calculator(AdderServiceClient())
    assert calc.add(1, 2) == 3

# This test fails with
> Exception: This calls an external service and should not be used in tests
```

Now, bring in some contextmanager magic here.
```python
from contextlib import contextmanager

@contextmanager
def mocked_adder_service_client():

    # Define a function that can add without
    # calling an external service.
    # The _ here is a placeholder for self
    def mocked_add(_, a, b):
        return a + b

    # Take a backup of the add in AdderServiceClient
    real_add = AdderServiceClient.add
    # Replace the add in AdderServiceClient with our mock function
    AdderServiceClient.add = mocked_add

    yield

    # Cleanup. Put back the real add
    AdderServiceClient.add = real_add

def test_calculator_adds():
    calc = Calculator(AdderServiceClient())
    with mocked_adder_service_client():
        assert calc.add(1, 2) == 3
```
We have a passing test case now.
