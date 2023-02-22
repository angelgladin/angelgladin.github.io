---
title: "Convert Sorted Array to Binary Search Tree"
date: 2022-12-25T18:37:58-06:00
draft: false
math: true
---

Most of the time, when writing code for a Technical Interview or just to _brush up_ on Data Structures
\& Algorithms skills, sometimes is forgotten two important aspects: *Code Quality* and
being [idiomatic](https://softwareengineering.stackexchange.com/a/94567) in the Programming
Language being used.

That's why I decided to write this post.

**This post will go through some [Kotlin](https://kotlinlang.org/) concepts.

# Understanding the problem

The [LeetCode problem](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree) states the following,

> Given an integer array `nums` where the elements are sorted in ascending order, convert it to a 
height-balanced[^1] binary search tree.

Before we start off, there are some important observations,

- `nums` is already sorted.
- An [Array](https://en.wikipedia.org/wiki/Array_(data_structure)) is given as an **input**. So
  that, accesing to an element will be a constant-time operation $O(1)$.
- The length of `nums` is at most $10^4$ so the time-complexity must be **a good** one.
- The resulting Binary Search Tree must be **height-balanced**.

----

That's the LeetCode definition of a Binary Tree,

```kotlin
class TreeNode(var `val`: Int) {
    var left: TreeNode? = null
    var right: TreeNode? = null
}
```

## Algorithm (Recursive solution)

Here's when [**Divide and Conquer**](https://www.geeksforgeeks.org/divide-and-conquer/) technique comes into play.

These are the steps to follow to create the BST,

1. Let $n$ the size of the array $A$ and $m = n/2$ the index of the middle element, and pick $A[m]$ as the __root of the BST[^2]__.
2. Consider the two following contiguous subarrays; $A[\ldots m-1]$ and $A[m+1 \ldots]$ respectively with $A$ as a $0$-indexed array, the left and right sub contiguous subarrays.
    * Create the left and right subtrees with $A[\ldots m-1]$ and $A[m+1 \ldots]$ sub contiguous subarrays recursively following the step __(1)__.
    * Repeat recursively until an empty subarray is encountered.

The following diagram depicts the idea,

![ ](/images/convert-sorted-arrray-to-binary-search-tree-kotlin.png)

# Kotlin Implementation

```kotlin
class Solution {
    fun sortedArrayToBST(nums: IntArray): TreeNode? {
        fun makeTree(start: Int, end: Int): TreeNode? =
            if (start >= end) {
                null
            } else {
                val mid = start + (end - start) / 2

                TreeNode(nums[mid]).apply {
                    left = makeTree(start, mid)
                    right = makeTree(mid + 1, end)
                }
            }

        return makeTree(0, nums.size)
    }
}
```

## Code Analysis

- Kotlin provides a [Null safety](https://kotlinlang.org/docs/null-safety.html) in the type system. That's the reason the type is `TreeNode?` instead of `TreeNode`. **We can know when a value is nullable or not at compile-time.**
- A [Local function](https://kotlinlang.org/docs/functions.html#local-functions) `makeTree` was created for building the tree. Note that `nums` is accessible inside the `makeTree` function.
- The `start` and  `end` are used as delimiters in the Binary Search. The reason is to avoid the creation of new subarrays.
- When performing a Binary Search within a range, there's a common mistake that leads to integer overflow[^3].
    That snippet would cause an overflow
    ```kotlin
    val mid = (start + end) / 2
    ```
    In order to avoid overflow, the index `mid` is computed as,
    ```kotlin
    val mid = start + (end - start) / 2
    ```


- In Kotlin, `if` is an [expression](https://kotlinlang.org/docs/control-flow.html#if-expression), it returns a value. Taking advantage of that property, `makeTree` either returns an empty node (`null`) or a `TreeNode`.
    * Returns `null` if there isn't a valid range to look up in the Binary Search.
    * A new `TreeNode` having as element the `mid` as value and, the left and right subtrees are built recursively.
- The funtion [`apply`](https://kotlinlang.org/docs/scope-functions.html#apply) is a [Scope function](https://kotlinlang.org/docs/scope-functions.html). Is used for assigning the values of the `left` and `right` subtrees. It's convenient when modifying the properties of a class in a Kotlin-idiomatic way.
    ```kotlin
    TreeNode(nums[mid]).apply {
        left = makeTree(start, mid)
        right = makeTree(mid + 1, end)
    } 
    ```

## Complexity Analysis

- **Time complexity**: $O(n)$, where $n$ stands for the size of the array.
- **Space complexity**: $O(h)$, where $h$ stands for the height of the BST. The maximum depth of the recursion in the stack is $h$.

> __Ergo, the BST is built in linear time and the space needed is $h$.__


[^1]: A **height-balanced** binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.
[^2]: For the sake of simplicity, _BST_ will stand for Binary Search Tree.
[^3]: [Extra, Extra - Read All About It: Nearly All Binary Searches and Mergesorts are Broken](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html)
