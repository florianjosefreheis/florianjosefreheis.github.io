---
title: "Learning Python"
layout: post
date: 2018-06-08 22:48
image:
headerImage: false
tag:
- python
- coursera
- debugging
category: blog
author: flo
description: First interaction with the programming language Python
---
---
## Learning Python

At the Begin of May I decided to extend my programming language toolbox by learning Python. The [Coursera Python Specialization](https://www.coursera.org/specializations/python) by University of Michigan made a great first impression to learn the basics of Python.

In about three weeks I've learnt the difference between Lists, Tuples, and Dictionaries. In the final part of the course had the goal to design and create your own python application for data retrieval, processing, and visualization. A capstone project that allows to use the new gained knowledge in a real world case.

### My three takeaways
1. **Indentation** is key and the most distinctive features of Python  to indent blocks of code.
2. **Debugging tools and methods** are essential and should be included at the beginning of every programming language course.
3. Python is a very **powerful language** (easy to learn, user-friendly, widely used, runs on (almost) any platform) that paves the way for new, exciting directions to explorer, especially via  all the scientific libraries.

One important part I was missing in the course was how you actually debug or trace a error. After looking around on Google and StackOverflow I found a very useful debugging tool called Python debugger **pdb**. This debugger is  equivalent to Ruby's **byebug** and easy to use.

It is essential to learn how to debug/trace errors right at the beginning when diving into a new programming language. It helps to understand what is going on, how the nature of the language works, and will make your life way easier to understand the bigger picture.

Run a python script from the command line by using the Python debugger:
{% highlight python %}
  python -m pdb my_script.py
{% endhighlight %}

Import the Python debugger at the top of the file:
{% highlight python %}
  import pdb;
{% endhighlight %}

Set the breaking point anywhere in your file by:
{% highlight python %}
  pdb.set_trace()
{% endhighlight %}

Read more about how to become a pdb-power user on [Medium](https://medium.com/instamojo-matters/become-a-pdb-power-user-e3fc4e2774b2).

This course has sparked the curiosity in me to use Python to explore  and extent my knowledge further in building.

You can find the my capstone project on [Github](https://github.com/florianjosefreheis/tree_register_vienna).

### One fun eater egg for the end

Type following in your Python console.

{% highlight unix %}
  >>> import this
  The Zen of Python, by Tim Peters

  Beautiful is better than ugly.
  Explicit is better than implicit.
  Simple is better than complex.
  Complex is better than complicated.
  Flat is better than nested.
  Sparse is better than dense.
  Readability counts.
  Special cases aren't special enough to break the rules.
  Although practicality beats purity.
  Errors should never pass silently.
  Unless explicitly silenced.
  In the face of ambiguity, refuse the temptation to guess.
  There should be one-- and preferably only one --obvious way to do it.
  Although that way may not be obvious at first unless you're Dutch.
  Now is better than never.
  Although never is often better than *right* now.
  If the implementation is hard to explain, it's a bad idea.
  If the implementation is easy to explain, it may be a good idea.
  Namespaces are one honking great idea -- let's do more of those!
  >>>
{% endhighlight %}

You can read those aphorisms up on the official [Python website](https://www.python.org/dev/peps/pep-0020/).
