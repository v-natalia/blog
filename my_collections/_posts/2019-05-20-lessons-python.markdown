---
layout: post
title:  "Lessons learnt while transitioning from Ruby to Python"
date:   2019-05-20 09:22:08 +0200
moment: 20th May, 2019
categories: programming
permalink: /posts/lessons-Py
bg: rails.jpg
---


Python.

Yes, now I'm learning Python, because I want to improve my atractiveness as a potential employee, and Python is sexy and everybody knows that.
Even if many people says that the transition from my former language (Ruby) to Python it's smooth I've found some pitfalls that I will keep updating here as I found them:

#1 Indentation is key.
While in Ruby an indentation issue is just messy and it could make you lose your time looking for the "end" that you forgot to write, in Python a mistake in the indentation is an error and your program will not run. So, indent or die.

#2 You are not allowed to interpolate strings and assign them to a variable.
In Ruby you could write:

`variable = "Hello" + " " + "world"`

But in Python, you will get a *funny* Error:

`'str' object does not support item assignment.`

What does that mean ? Strings are inmutable on Python so to do the same as you did in Ruby, you should write 5 lines of code to append every string to a List ( or array in Ruby `[]`) and then join the whole thing and put it in the variable you wanted. (That's one way of solution).

#3 Dictionary keys have some restrictions.
But first, a dictionary is like this:
`dictionary = {key1: value1, key2: value2}`
- So, in a dictionary a key can appear only once. If it appears a second time, then you will be reassigning it.
- A dictionary key must be of an inmutable type, like... strings, also integers, floats or booleans are inmutables in Python. But lists, they are not inmutables, so you can not do something like this:
`dictionary = {[key1, key2]: 'value1', [key3, key4]: 'value2'}`
