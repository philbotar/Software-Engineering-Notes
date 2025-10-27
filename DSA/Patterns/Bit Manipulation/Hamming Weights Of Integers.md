# Hamming Weights of Integers

The hamming weight of a number is the number of set bits (1 bits) in its binary representations. Given a positive integer n, return an array where the ith element is the Hamming Weight of integer i for all integers from 0 to n.

Input: n = 7
Output: [0, 1, 1, 2, 1, 2, 2, 3]

| Number | Binary Representation | Number of Set Bits |
| ------ | --------------------- | ------------------ |
| 0      | 0                     | 0                  |
| 1      | 1                     | 1                  |
| 2      | 10                    | 1                  |
| 3      | 11                    | 2                  |
| 4      | 100                   | 1                  |
| 5      | 101                   | 2                  |
| 6      | 110                   | 2                  |
| 7      | 111                   | 3                  |


### Intuition

As a simple way to figure out how to get the hamming weights, we know that if we do an AND on each 1 with another 1, we'll know for certain that its a 1. 

Knowing this, this means for each number we can do a right shift with an & on 1 to find out if that value is a 1 or not. The good thing about doing a right shift is that it means we can go to the next value until its 0. 

So we: 
1. Iterate from 1 to n
2. while the value is > 0, compare its current rightmost bit to 1.
3. If theyre both 1, add to count. 


This is the simple way of doing it, but we know that theres an optimal substructure that we can use.