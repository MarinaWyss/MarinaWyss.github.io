---
title: "Understanding Recursion"
date: 2020-06-07
tags: [computer science, python]
header: 
  image: "/images/recursion/recursion.png"
excerpt: "The secret was the call stack!"
---

## What I Learned: Recursion and Memoization

I was first introduced to the concept of recursion in my Intro to Python class. The basic idea is pretty simple: Recursion is when a function calls itself. 

But I noticed that I’ve struggled to put it into practice, and even to really understand what is happening behind the scenes in a recursive function. Finally this week, after working through week #4 of Harvard’s Intro to CS course [(CS50)](https://cs50.harvard.edu/x/2020/), I think I’ve figured it out. The missing piece of the puzzle was understanding the call stack!


**Recursive Functions**
A recursive function has two cases that could apply, given any input:
* Base Case: The set of circumstances where a function will stop. There must be at least one base case specified in every recursive function, otherwise it could technically go on forever (at least until a “stack overflow” takes place). 
* Recursive Case: The part where the function calls itself.


**The Call Stack**
When a function is called, some memory is set aside to actually do the operation. This is referred to as “pushing a function frame onto the call stack.” It is possible to have more than one function frame in memory at once, though typically only one is active at any given point in time. 

A stack is a data structure that only lets you interact with the topmost item. When a new function is called, a new frame is pushed to the top of the stack, and it becomes the active frame. When a function finishes its work, its frame is popped off the stack, and the frame immediately below it becomes the new, active frame. This next function picks up immediately where it left off. 

It’s helpful to think of the stack like a list in Python, where adding an item to the top (“pushing”) is like the Python append method, and removing something from the top is popping. 

To illustrate recursion and the call stack a bit further, I’ll go through two common examples of recursive functions: 


**Factorial**

The basic recursive factorial function is as follows:
```python
def fact(n):
	if n == 1:
		return 1
	else:
		return n * fact(n - 1)

print("The factorial of 5 is:", fact(5))
```
*(This is a simplified function that doesn't worry about checking for negative numbers, or things like that).*

In this example, `n == 1` is the base case, and `n * fact(n - 1)` is the recursive case. If we print `fact(5)`, the call stack could look as follows:

fact(1) - base case
fact(2)
fact(3)
fact(4)
fact(5)
print()


**Fibonacci**

The basic recursive fibonacci functions looks like:

```python
def fib(n):
	# base case
	if n == 1 or n == 2: 
		return 1
	# recursive case
	else:
		return fib(n - 2) + fib(n - 1) 

print("The 5th fibonacci number is:", fib(5))
```
A breakdown of what happens here is:
1. We first call print,
2. Which calls fib(5),
3. Which calls fib(4), then fib(3), then fib(2), 
4. Then finally fib(1), which returns one
5. At this point fib(1) is popped off the call stack, and the functions down the stack can start returning one at a time.
6. Finally, we print the result.

(This video)[https://www.youtube.com/watch?v=AfBqVVKg4GE] breaks it down quite nicely!


**When to Use Recursion**

Recursive functions are, obviously, quite nifty. They can result in code that is shorter and easier to understand, and are often practical when working on problems with a tree-like structure that require backtracking.

But, they can also be quite inefficient due to repeating functions over and over. In many cases, an iterative solution is preferable. 


**Memoization**
Memoization is an option for making recursive functions more efficient. According to Wikipedia, “In computing, memoization or memoisation is an optimization technique used primarily to speed up computer programs by storing the results of expensive function calls and returning the cached result when the same inputs occur again.” 

Basically, it means that we remember the results from a particular function given a certain input, and in the future we simply access this value rather than running the function again. 

We can implement this on the Fibonacci example as follows:

```python
# manual version
fib_cache = {}

def recursive_fib(n):
	if n in fib_cache:
		return fib_cache[n]
	if n == 1 or n == 2: 
		return 1
	else:
		fib_cache[n] = fib(n - 2) + fib(n - 1) 

	return fib_cache[n]

# automatic version
import functools

@functools.lru_cache()
	if n == 1 or n == 2: 
		return 1
	else:
		return fib(n - 2) + fib(n - 1) 
```



