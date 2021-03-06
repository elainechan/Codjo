# Optimizing

During the code review for [Problem1](https://github.com/reeddunkle/Codjo/tree/master/Problem1_Richie_Rich) we mentioned the idea of optimizing. You'll hear people talk about this all the time, and I'd like to demystify it and help you improve your code.

**Note:** In my examples I'm using Python3. For the Python2 equivalent, use `xrange` instead of `range`.

Big O
----

You've probably heard people talk about "big O notation". It's used to indicate how an algorithm responds to different input sizes:

[Wikipedia](https://en.wikipedia.org/wiki/Big_O_notation):
> In computer science, big O notation is used to classify algorithms by how they respond to changes in input size, such as how the processing time of an algorithm changes as the problem size becomes extremely large.

This is what we're getting at when we talk about an algorithm's "complexity".


Complexity
----

Borrowing from the above definition, we can say that an algorithm's complexity measures how the function responds when you change the input size, which is another way of defining its performance. The goal of optimizing, then, is to reduce that response and improve its performance.


Time
----

Look at this example of `is_palindrome` from [Problem1's tests](https://github.com/reeddunkle/Codjo/blob/master/Problem1_Richie_Rich/richie_rich_test.py):

```python
def is_palindrome(text):
    for i in range(len(text)//2):
        if text[i] != text[-1-i]:
            return False

    return True
```

During the code review I brought up this idea of how much time the function requires, and I used the terms "time complexity", and "run time". These describe the same thing.

To measure its time complexity, I asked, "How does the function respond to inputs of different sizes?"

Let's say the input to `is_palindrome` is `1234554321`, and so `len(text) == 10`. Before it completes, the for-loop will assign to `i` the numbers `0, 1, 2, 3, 4`

You might point out that this function could exit the loop midway with its `return False` statement, and you're right. However, that's out of our control. We're almost always trying to improve the worst case scenario.

If _n_ represents the size (length) of the input, in the worst case scenario this function needs to loop _n/2_ times.

When this came up in our discussion, someone said that they hadn't realized until later that they only needed to iterate through half of the input. Instead they used something like this:

```python
def is_palindrome(text):
    for i in range(len(text)):
        if text[i] != text[-1-i]:
            return False

    return True
```

With the same input, the function now needs to loop _n_ times (where _n_ is the size of the input).

Despite these being toy examples, addressing this problem of "Given an input, how many times do I need to do something?" is a real-life consideration for real-life programs.

There are a few more terms that people use when talking about a function's complexity. For the example above where the function has to iterate _n_ times, they say that it has a **linear** time complexity (or run-time), because the time complexity grows linearly with its input size. The "Big O" notation for this is `O(n)`.

In the first example you only have to iterate _n/2_ times, so you would think that its complexity is `O(n/2)`. However, we're measuring how fast the complexity grows with its input, and it grows linearly. This isn't obvious at all.

Here's a similar example, with the input `[0, 4, 6, 3, 2, 7, 5, 1, 8, 8]`:

```python
def print_twice(numbers):
    for num in numbers:
        print(num)

    for num in numbers:
        print(num)
```

Each for-loop requires _n_ iterations, and there are two for-loops, and it seems like its time complexity would be `O(2n)`. It isn't, however, it's still just `O(n)`.

When I've asked people to explain this, they point out that constants aren't very important when talking about really big numbers:

> You want to use big O when thinking about how the performance is going to change with big (really big) inputs. There will be a point where _n²_ might be as good as, say, _100n_, but at some point when the input size changes enough, it will start to be worse.

This is true, but I think this answer is a little unsatisfying. It misses part of what we're trying to analyze.

We want to measure how the function responds to different inputs, how it _changes_. When you frame it like this, it's easier to see that we can drop the constant because the constant doesn't change with the input.

Now take this example with the same input `[0, 4, 6, 3, 2, 7, 5, 1, 8, 8]`:

```python
def bubble_sort(numbers):
    for i in range(len(numbers)):
        for k in range(len(numbers)):
            if numbers[i] < numbers[k]:
                numbers[i], numbers[k] = numbers[k], numbers[i]

    return numbers
```

`numbers` has a length of 10, so `i` will be assigned each number from `0...10`, and `k` will also be assigned each number from `0...10`. The catch is that `k` is going to perform `n` operations for every one of `i`.

`i` is going to start of at `0`, and then `k` will go through the loop. Then `i` will become `1`, and `k` will going through the loop, etc.

This is `O(n²)`, which grows at a **quadratic** rate.

To clear up some of this confusion, Leo said:
> In absolute efficiency terms, _n/2_ iterations is preferable to _2n_, and the former should definitely be used over the latter. But in relative terms, they grow at more or less the same speed, which means that they are both preferable (at some point) over _n²_.


There are common benchmarks for different types of algorithms. For example, searching through a sorted list can be performed in **logarithmic** time, `O(log(n))`. I'll cover this when we go over [Problem2](https://github.com/reeddunkle/Codjo/tree/master/Problem2_Sorted_Search).

We'll do sorting algorithms in the near future. For now, you should be aware that [Bubble Sort is notoriously bad](https://youtu.be/k4RRi_ntQc8) at `O(n²)`, and that better algorithms achieve `O(n log(n))`.


The last one I'm going to cover before talking about space complexity is **constant**, which is `O(1)`. This means no matter how the input changes, your function's performance is contant.

Here's a short, pointless example using the same input as above, `[0, 4, 6, 3, 2, 7, 5, 1, 8, 8]`:

```python
def print_first_five(numbers):
    for i in range(5):
        print(numbers[i])
```

It wouldn't matter if the input size were 200 instead of 10. It's just going to print the first five numbers in the list. This function's complexity responds to changes in input size at a constant rate, `O(1)`.


Space
----

Space complexity is talked about in the same way as time complexity.

Your function's space complexity refers to how your function's space requirements change as you change the input size.

I'll go through some simple examples. For all of them, let's assume the input is still `[0, 4, 6, 3, 2, 7, 5, 1, 8, 8]`.

First, two examples of constant space:

**`O(1)`**

```python
def constant_space(numbers)
    new_list = []
    for i in xrange(5):
        new_list.append(numbers[i])

    return new_list
```

and...

```python
def bubble_sort(numbers):
    for i in range(len(numbers)):
        for k in range(len(numbers)):
            if numbers[i] < numbers[k]:
                numbers[i], numbers[k] = numbers[k], numbers[i]

    return numbers
```

The first function creates a new list and adds the first five numbers from the input to it. No matter how large `numbers` is, the function only requires a constant 5-length list to be held in memory.

`bubble_sort` on the other hand requires no additional memory. It destroys the original input by mutating it in place (which is something you'll hear people criticize), but in return it doesn't take up any additional space.

**`O(n)`**

```python
def linear_space(numbers)
    new_list = []
    for i in range(n):
        new_list.append(numbers[i])

    return new_list
```

This `linear_space` function requires an n-length list to be held in memory, as it's simply copying the current input. The larger the input, the larger `new_list` will swell.


Time vs. Space
----

It's common to find time and space competing with one another. People in every field talk about "design trade-offs". In CS, there tends to be a trade-off between time and space. You definitely want to minimize both, but at some level you'll probably find yourself facing design trade-offs.

An example where you face a trade-off is with caching. A cache holds in memory some information that you've already spent time calculating, in order to speed up retrieving that information the next time you want it. Given the same input, it spares you the run-time. In other words, you're using extra space to increase speed.

At one end of the spectrum, you could imagine a cache that holds the result of every calculation your function might perform. Its space complexity would be infinite, but its time complexity given this (idealistic) cache could be constant.

On the other end of the spectrum, imagine that there is no cache, and so your function has to run its calculations every time. You save space at the cost of time.
