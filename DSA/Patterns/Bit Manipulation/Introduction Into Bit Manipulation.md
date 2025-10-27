# Bit Manipulation

Bit Manipulation is the act of programmers manipulating the bits within an integer, using it as an array / store without having the overhead of an actual array. It acts as a highly efficient resizable boolean array.

## Bitwise Operators

There are a few operators that exist that we can use to manipulate these representations .

#### NOT (~)

Not is represented by ~var, and what it does is flip each bit within the number. When you perform a flip on a positive number, it will flip it to negative due to Twos Complement.

a = 01, ~a = 10

#### AND (&)

And works how you would think, when you perform and on two numbers, every bit in both is compared, and the resulting is the and of all bits in the number.

a = 0011
b = 0101
a & b = 0001

#### OR (|)

OR is the same, where it allows you to compare each bit in two integers and perform OR on each bit.

a = 0011
b = 0101
a | b = 0111

#### XOR (^)

XOR is like or but if theres two 1's it will set it as zero rather than a 1.

a = 0011
b = 0101
a ^ b = 0110

Some useful characteristics of XOR are
a ^ 0 = a
a ^ a = 0

#### Left Shift (<<)

Left shift is shifting the bits in an integer to the left by x, adding zeros to the right. This is equivalent to multiplying a number by 2^n.

So for example

a = 1001 (9)
a << 2 = 100100 (9 x 2^2) -> (9 x 4) -> 36

#### Right Shift (>>)

Shifts bits of a number to the right by n positions, discarding bits on the right. This is equivalent to dividing a number by 2^n.

e.g
a = 100100 (36)
a >> 2 = (36 / 4) = 9

#### Useful Operations

- Setting the ith bit of x to 1: x |= (1 << i)
- Clearing the ith bit of x: x &= ~(1 << i)
- Toggling the ith bit of x (from 1 to 0 or vice versa): x ^= (1 << i)
- Checking if a number x is even or odd: if (x & 1 == 0), x is even
- Checking if a number is a power of 2: if x > 0 && x & (x - 1) == 0
