# Problem: Fraction to Recurring Decimal

#### Date: 20/12/2025

#### Pattern Category: The pattern you identified (e.g., Arrays & Hashing, Two Pointers).

## My Flawed Approach: Tried to use doubles, got stuck when output was too long

## The Key Insight: 
When you divide two integers, sometimes you get a clean decimal (like 1/2 = 0.5), but other times the decimal keeps repeating forever (like 1/3=0.3). The trick here is to simulate long division and notice when remainders start repeating.

### Optimal Code
```java
class Solution {
    public String fractionToDecimal(int numerator, int denominator) {
        if (numerator == 0) {
            return "0";
        }
        StringBuilder fraction = new StringBuilder();
        // If either one is negative (not both)
        if ((numerator < 0) ^ (denominator < 0)) {
            fraction.append("-");
        }
        // Convert to Long or else abs(-2147483648) overflows
        long dividend = Math.abs(Long.valueOf(numerator));
        long divisor = Math.abs(Long.valueOf(denominator));
        fraction.append(String.valueOf(dividend / divisor));
        long remainder = dividend % divisor;
        if (remainder == 0) {
            return fraction.toString();
        }
        fraction.append(".");
        Map<Long, Integer> map = new HashMap<>();
        while (remainder != 0) {
            if (map.containsKey(remainder)) {
                fraction.insert(map.get(remainder), "(");
                fraction.append(")");
                break;
            }
            map.put(remainder, fraction.length());
            remainder *= 10;
            fraction.append(String.valueOf(remainder / divisor));
            remainder %= divisor;
        }
        return fraction.toString();
    }
}
```

#### Complexity: Time: O(n), Space: O(n). You must know this. No exceptions.
