### 剑指 Offer 53 - I. 在排序数组中查找数字 I

>统计一个数字在排序数组中出现的次数。
***
>示例 1:  
>输入: nums = [5,7,7,8,8,10], target = 8  
>输出: 2  

>示例 2:  
>输入: nums = [5,7,7,8,8,10], target = 6  
>输出: 0  
***
```java
class Solution {
    public int search(int[] nums, int target) {
        return (helper(nums, target) - helper(nums, target - 1));
    }

    private int helper(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] <= target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return left;
    }
}
```