# Exception Flow
## About
Python 3.11 seems to have been arguably the single greatest release for error-handling in Python's history. It completely changed the game by introducing these goodies:
* ExceptionGroup (PEP 654)
* except* clauses (PEP 654)
* add_note() and the hidden __notes__ attribute (PEP 678)
* Fine-grained error highlights (The ^^^^ arrows in tracebacks)

There are many ways that exceptions can be used and dealt with. The goal of this TIL is to focus on highlighting what stands out about each possible basic "Lego block" so that you can pick the right ones for your coding tasks.

## Overview
Below, I'm exploring the **ExceptionGroup** object and the **except\* clause** by contrasting and/or combining them with traditional **manual exceptions** and traditional **except clauses** to see how Python behaves:
* **Example 1** demonstrates *the old way* using traditional **manual exceptions** and one or more traditional **except clauses**.
* **Example 2** demonstrates *a hybrid way* using an **ExceptionGroup** and one or more traditional **except clauses**.
* **Example 3** demonstrates *a hybrid way* using traditional **manual exceptions** and an **except\* clause**.
* **Example 4** demonstrates the new way using an **ExceptionGroup** and an **except\* clause**.

## Disclaimers
* In some examples below, a massive stack of exceptions are manually shoved into one try block. This isn't standard practice and you'd normally test them via separate try blocks, loops, or conditional logic. More than one error **can** occur within a single block of code, however, with those examples simulating that likelihood as a visual contrast to the examples that don't use them.
* In some examples below, raising an **ExceptionGroup** seems like peaceful "legal tender" that's offered to Python in exchange for the sub-exceptions that you're really after while it squirrels away your exception as though it never happened. However, it **is** an exception that you call forth to magically raise all of its sub-exceptions concurrently. And, of course, Python doesn't sweep it under the rug, but the good news is that since you conjured it, it's wanted.

## Example 1 - traditional manual exceptions with a traditional except clause
This example demonstrates how Python behaves when you use traditional **manual exceptions** with a traditional **except clause**. When a traditional **except clause** is used, Python only catches the **first** error since traditional sequential execution stops on the first crash. You'd have to fix each error to see any remaining errors.
```python
try:
    # Test some code:
    foo               # NameError
    print(1 + "two")  # TypeError
    int("abc")        # ValueError
    print(5 / 0)      # ZeroDivisionError
# Catch and handle a maximum of ONE error (the first if many):
except Exception as err:
    print(err)
```
* Example 1 output:
  ```bash
  name 'foo' is not defined
  ```
 
## Example 2 - an ExceptionGroup with a traditional except clause
This example demonstrates how Python behaves when you use an **ExceptionGroup** with a traditional **except clause**. Since a traditional **except clause** is blind to the individual sub-exceptions inside of a group, it treats the group as a single object.
```python
# Create an ExceptionGroup populated with a list of error-types and their descriptions:
eg = ExceptionGroup('My Group',
        [
        NameError("invalid name"),
        TypeError("invalid type"),
        ValueError("invalid value"),
        ZeroDivisionError("invalid operation"),
        ]
    )
# Test some code:
try:
	raise eg
# Catch and handle a maximum of ONE error (the first if many):
except Exception as err:
	print(err)
```
* Example 2 output:
  ```bash
  My Group (4 sub-exceptions)
  ```
 
## Example 3 - traditional manual exceptions with an except* clause
This example demonstrates how Python behaves when you use traditional **manual exceptions** with an **except\* clause**. When traditional **manual exceptions** are used, Python only catches the **first** error since traditional sequential execution stops on the first crash. You'd have to fix each error to see any remaining errors.
```python
# Test some code:
try:
    # Test some code:
    foo               # NameError
    print(1 + "two")  # TypeError
    int("abc")        # ValueError
    print(5 / 0)      # ZeroDivisionError
# Catch and handle ALL of the errors concurrently:
except* Exception as gathered:
    for exc in gathered.exceptions:
        print(f"📦 Error caught: {type(exc).__name__}")
```
* Example 3 output:
  ```bash
  📦 Error caught: NameError
  ```

## Example 4 - an ExceptionGroup with an except* clause
This example demonstrates how Python behaves when you use an **ExceptionGroup** with an **except\* clause**. When these are paired together, you can create as many errors as you like in the try block by bundling them all up in an **ExceptionGroup** and all of them will be handled at once, concurrently, in the **except\* clause** that prevents Python from stopping at the first one.
```python
# Create an ExceptionGroup populated with a list of error-types and their descriptions:
eg = ExceptionGroup('My Group',
        [
        NameError("invalid name"),
        TypeError("invalid type"),
        ValueError("invalid value"),
        ZeroDivisionError("invalid operation"),
        ]
    )
# Test some code:
try:
    raise eg
# Catch and handle ALL of the errors concurrently:
except* Exception as gathered:
    for exc in gathered.exceptions:
        print(f"📦 Error caught: {type(exc).__name__}")
```
* Example 4 output:
  ```bash
  📦 Error caught: NameError
  📦 Error caught: TypeError
  📦 Error caught: ValueError
  📦 Error caught: ZeroDivisionError
```
