---
title: "Python Generators"
date: 2021-02-15
tags: [python]
header: 
  image: "/images/iterators_generators/iterators_generators.png"
excerpt: "Paths to faster code."
---

Header image from [this useful blog](https://nvie.com/posts/iterators-vs-generators/).

As the scale of the projects I’m working on has increased, I’ve been thinking more about code efficiency. Today I wanted to spend some time getting clear on generators as a form of iterator, and how they might come in handy.

Generally, iterators are useful for larger-scale projects because of lazy evaluation: Rather than computing the value of each item when instantiated, iterators only evaluate expressions when the values are needed, and avoid repeated evaluations. This saves memory, and allows for complete data to be worked on while the rest is still being evaluated.

Stepping back to the basics, an iterable is an object capable of returning its members one at a time. For example, lists, strings, or dictionaries. For something to be iterable, it needs to have the `__iter__` special method. 

When an iterable object is passed as an argument to the built-in function `iter()`, it returns an iterator for the object. This happens in the background when using a for loop, for example. 

Iterators are objects with a state that remembers the current and next values during iteration. Iterators are defined by a class Iterator Protocol, which has both an `__iter__` and `__next__` special method. Each `next()` call gets the next item in the sequence until the iterator is exhausted, and `StopIteration` is raised.

Generators are functions that return iterators. They look pretty much the same as a typical function, except that they end with `yield` instead of `return`. When the `yield` statement is executed, the program suspends the current function execution, and returns the yielded value. The generator saves the state of the function, and picks up where it left off when/if it is called again. This can be done as many times as desired, until reaching the end of the sequence, while storing only a single value at a time. 

For example:

```python
def squares(num_list):
	for i in num_list:
		yield i * i

num_list = [2, 3, 4]

res = squares(num_list)

print(next(res)) # 4
print(next(res)) # 9
print(next(res)) # 16
```

Generators can also be used similarly to list comprehension, using generator expressions:

```python
res = (x * x for x in num_list)
```
