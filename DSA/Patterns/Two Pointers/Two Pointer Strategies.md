# Two Pointer Strategies


### Inward/Converging Traversal

You initialise two pointers. One at the beginning and one at the end of the array. Based on conditions, you decide whether to increment the left pointer, or decrement the right. You do this until a condition is met, or the two pointers meet.

This is great for problems where we need to compare elements from two sides of the array.


### Unidirectional Traversal

When you have two points that start at the same end of the data structure, and move in the same direction. Usually this is for when the pointer in front is looking for information, and the one behind is tracking certain info.


### Staged Traversal

First you set up your right pointer. You let it iterate till it finds whatever its looking for. Then you increment your left pointer to add additional info until a condition is met.
