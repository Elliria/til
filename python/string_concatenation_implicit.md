# Implicit String Literal Concatenation in Python

### About
A brief overview of adjacent string literal handling and line wrapping in Python based on official standards.

### Overview
Yesterday, I encountered this snippet that uses **explicit line continuation** (or **juxtaposition**), where Python automatically fuses adjacent string literals separated only by white-space into a single string object at compile-time: 
```python
wildcard = "Markdown file (*.md)|*.md|" \
        "HTML file (*.html)|*.html"
```

At first glance, it seems perfectly normal, but when I looked closely at that snippet, I spotted something rather unusual. This was **not** an ordinary **string concatenation** with an **explicit line continuation**, which would consist of **one** string on multiple lines with a backslash used to trigger **explicit line continuation**. Instead, this was **two** strings, each on separate lines with a backslash at the end of the first one!

That didn't look valid to me because, with no comma, it sure looks like two distinct **strings** rather than a **tuple**. If you remove the backslash and bring the second line up, you get this strange-looking value (note the space between the two completely separate strings):
```python
wildcard = "Markdown file (*.md)|*.md|" "HTML file (*.html)|*.html"
```

Joining two completely separate strings together with an **explicit line continuation** (a backslash) is called **explicit string literal concatenation**. It seems that you can use multiple strings as a single value and Python will do the concatenation for you.

It gets even better. The backslash is **optional** if you surround the collection of strings in **parentheses**, which is the preferred [PEP 8](https://peps.python.org/pep-0008/#maximum-line-length) way to continue lines without backslashes. Even `("foo")` all by itself is a string, parentheses and all! When working with multiple strings, each can go on its own line and the backslash is simply **implied** without being there. This is called **implicit string literal concatenation**

As icing on the cake, you can include one or more variables to be concatenated with the string(s). Because string concatenation happens at compile-time, raw variables can't be placed directly next to string literals, but f-strings can, since they're evaluated as string literals during lexical analysis!

In every one of these cases, Python reads all of the strings as one single string-object.

What intriguing ways to control white-space or separate chunks of text visually while Python keeps them together for you! This would be incredibly useful for long SQL queries, regex patterns, deep file-paths, or long URLS, where you want to keep your code readable without injecting accidental or unwanted white-space.

### 💡 Examples of concatenating strings
* Example 1 - string:
  ```python
  foo = "hello world"
  print(foo)  # Output: hello world
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 2 - string concatenation:
  ```python
  foo = "hello \
         world"
  print(foo)  # Output: hello        world
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 3 - implicit string literal concatenation:
  ```python
  print("\nEXAMPLE 3 - implicit string literal concatenation:")
  foo = "hello" "world"
  print(foo)  # Output: helloworld
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 4 - implicit string literal concatenation and explicit line continuation:
  ```python
  foo = "hello" \
        "world"
  print(foo)  # Output: helloworld
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 5 - implicit string literal concatenation:
  ```python
  foo = ("hello" "world")
  print(foo)  # Output: helloworld
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 6 - implicit string literal concatenation and implicit line continuation:
  ```python
  foo = (
          "hello"
          "world"
          )
  print(foo)  # Output: helloworld
  print(type(foo))  # Output: <class 'str'>
  ```
### 💡 Examples of concatenating strings and variables
* The variable used in the examples:
  ```python
  var = 1 + 1
  print(var)  # Output: 2
  print(type(var))  # Output: <class 'int'>
  ```
* Example 1 - implicit string literal concatenation:
  ```python
  foo = "hello" "world" f"{var}"
  print(foo)  # Output: helloworld2
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 2 - implicit string literal concatenation and explicit line continuation:
  ```python
  foo = "hello" \
        "world" \
        f"{var}"
  print(foo)  # Output: helloworld2
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 3 - implicit string literal concatenation:
  ```python
  foo = ("hello" "world" f"{var}")
  print(foo)  # Output: helloworld2
  print(type(foo))  # Output: <class 'str'>
  ```
* Example 4 - implicit string literal concatenation and implicit line continuation:
  ```python
  foo = (
          "hello"
          "world"
          f"{var}"
          )
  print(foo)  # Output: helloworld2
  print(type(foo))  # Output: <class 'str'>
  ```

### Summary
* Line continuation approaches:
  * The backslash triggers **explicit line continuation** from the use of a character to explicitly force the line to continue.
  * The parentheses trigger **implicit line continuation** from the use of placement on separate lines to imply that the line should continue.

### References
* The [Maximum Line Length](https://peps.python.org/pep-0008/#maximum-line-length) section in the **PEP 8 - Style Guide for Python Code** document states: "The preferred way of wrapping long lines is by using Python’s implied line continuation inside parentheses, brackets and braces. Long lines can be broken over multiple lines by wrapping expressions in parentheses. These should be used in preference to using a backslash for line continuation."
* The [Logical lines](https://docs.python.org/3/reference/lexical_analysis.html#logical-lines) section in the **Python Language Reference (Lexical Analysis)** document states: "The end of a logical line is represented by the token NEWLINE. Statements cannot cross logical line boundaries except where NEWLINE is allowed by the syntax (e.g., between statements in compound statements). A logical line is constructed from one or more physical lines by following the explicit or implicit line joining rules."

---
**Tags:** tag-concatenation tag-python tag-strings
