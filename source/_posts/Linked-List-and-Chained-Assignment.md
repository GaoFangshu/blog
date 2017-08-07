---
title: Linked List and Chained Assignment
date: 2017-07-09 14:01:15
tags: [Python, LeetCode]
---
I had lots of fun with [LeetCode](https://leetcode.com/problemset/all/) recently, and learned some basic stuff, e.g., [linked list](http://www.geeksforgeeks.org/linked-list-set-1-introduction/) and [chain assignment](https://stackoverflow.com/questions/11498441/what-is-this-kind-of-assignment-in-python-called-a-b-true).
<!--more-->

In problem ["Add Two Numbers"](https://leetcode.com/problems/add-two-numbers/#/description), we need to implement linked list in Python as `class ListNode`:
```python
class ListNode(object):
    def __init__(self, x):
        self.val = x
        self.next = None
```
With linked list, storing data doesn't have to set the length or size of it. If we want to add `1` after `2`, just `x = ListNode(1)` and `x.next = ListNode(2)`. The memory location of `x` won't be changed, which is a more efficient way to manage memory.

Another trick in "Add Two Numbers" is chained assignment. In my solution:
```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        result = inside = ListNode(0)
        one = 0
        while l1 or l2 or one:
            o1 = o2 = 0
            if l1:
                o1 = l1.val
                l1 = l1.next
            if l2:
                o2 = l2.val
                l2 = l2.next
            one, o = divmod(o1 + o2 + one, 10)
            inside.next = ListNode(o)
            inside = inside.next
        return result.next
```
Chained assignment is applied in `result = inside = ListNode(0)`. Then, `result` and `inside` are the same object (e.g., `<__main__.ListNode object at 0x0000024EE54D2B70>`). Object in Python is based on data, the name of a variable is just like a tag. Skip to `inside.next = ListNode(o)`, the `next` attribute of `<__main__.ListNode object at 0x0000024EE54D2B70>` is `ListNode(o)`. That is, the `next` attribute of `result` and `inside` are both `ListNode(o)` (e.g. `<__main__.ListNode object at 0x0000024EE54AAF60>`). It is worth noting that, after `inside = inside.next`, `inside` points to `0x0000024EE54AAF60`, and `result` is still `<__main__.ListNode object at 0x0000024EE54D2B70>` but has an attribute `next` at `0x0000024EE54AAF60`.

Thus, with chained assignment and linked list, we can do something in while-loop, and record result of each iteration to an object outside the loop.