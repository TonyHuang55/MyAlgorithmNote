### 剑指 Offer 59 - I. 滑动窗口的最大值

>给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。
***
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums.length == 0 || k == 0) return new int[0];
        int[] res = new int[nums.length - k + 1];
        Deque<Integer> stack = new LinkedList<>();
        for (int j = 0, i = 1 - k; j < nums.length; i++, j++) {
            if (i > 0 && stack.peekFirst() == nums[i - 1]) {
                stack.removeFirst();
            }
            while (!stack.isEmpty() && stack.peekLast() < nums[j]) {
                stack.removeLast();
            }
            stack.addLast(nums[j]);
            if (i >= 0) {
                res[i] = stack.peekFirst();
            }
        }
        return res;
    }
}
```