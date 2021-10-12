### 剑指 Offer 57 - II. 和为s的连续正数序列

>输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。
>
>序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。
***
```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        int left = 1, right = 2, sum = 3;
        List<int[]> res = new ArrayList<>();
        while (left < right) {
            if (sum == target) {
                int[] ans = new int[right - left + 1];
                for (int k = left; k <= right; k++) {
                    ans[k - left] = k;
                }
                res.add(ans);
            }
            if (sum >= target) {
                sum -= left;
                left++;
            } else {
                right++;
                sum += right;
            }
        }
        return res.toArray(new int[0][]);
    }
}
```