# Next Permutation 

A permutation of an array of integers is an arrangement of its members into a sequence or linear order.

For example, for arr = [1,2,3], the following are all the permutations of arr: [1,2,3], [1,3,2], [2, 1, 3], [2, 3, 1], [3,1,2], [3,2,1].
The next permutation of an array of integers is the next lexicographically greater permutation of its integer. More formally, if all the permutations of the array are sorted in one container according to their lexicographical order, then the next permutation of that array is the permutation that follows it in the sorted container. If such arrangement is not possible, the array must be rearranged as the lowest possible order (i.e., sorted in ascending order).

For example, the next permutation of arr = [1,2,3] is [1,3,2].
Similarly, the next permutation of arr = [2,3,1] is [3,1,2].
While the next permutation of arr = [3,2,1] is [1,2,3] because [3,2,1] does not have a lexicographical larger rearrangement.
Given an array of integers nums, find the next permutation of nums.

The replacement must be in place and use only constant extra memory.

 

Example 1:

Input: nums = [1,2,3]
Output: [1,3,2]


### Intuition
The intuition behind this is that you want to find the first decreasing value going from right to left. 

1. Find the Breakpoint (Right to left scan), where a[i] > a[i - 1]. This will be the pivot. 
2. Find the Swap Element: To ensure that the resulting permutation is the next largest, we swap the pivot a[i - 1] with the smallest element to its right that is just larger than a[i -1]. 
3. This will leave us with an array that isnt necessarily smaller, but we know that in order to get the next largest we need to reverse the remaining part of the array. 

### Intuition By Steps

1. Start with [1,4,2,5,3]
2. Find the swap element, which in this case is 2
3. Swap 2 with 3, since its the next largest smallest value. This leaves us with [1,4,3,5,2]
4. Now reverse everything from 3 onwards, which gives us [1,4,3,2,5]
5. Thats it!


### Complexity
TC: O(n) (reverse can only be max O(n), same with the while loop)
SC: O(1), since we only need i, j and temp. 


### Code
```java
class Solution {
    public void nextPermutation(int[] nums) {
        int ind1 = -1;
        int ind2 = -1;
        
        // Step 1: Find Breaking Point
        for(int i = nums.length - 2; i >= 0;i--){
            if(nums[i] < nums[i+1]){
                ind1 = i;
                break;
            }
        }

        // If there is no breakpoint
        if(ind1 == -1){
            reverse(nums,0);
        }
        
        else{
            // step 2 find next greater element and swap with ind2
            for(int i=nums.length-1;i>=0;i--){
                if(nums[i]>nums[ind1]){
                    ind2=i;
                    break;
                }
            }

            swap(nums,ind1,ind2);
            // step 3 reverse the rest right half
            reverse(nums,ind1+1);
        }
    }
    
    void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }

    void reverse(int[] nums,int start){
        int i = start;
        int j = nums.length-1;

        while(i<j){
            swap(nums,i,j);
            i++;
            j--;
        }
    }
}
```