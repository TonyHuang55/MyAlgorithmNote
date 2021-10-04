### 剑指 Offer 45. 把数组排成最小的数

>输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。
***
>示例 1:  
>输入: [10,2]  
>输出: "102"  

>示例 2:  
>输入: [3,30,34,5,9]  
>输出: "3033459"  
***
```java
class Solution {
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }
        quickSort(strs, 0, strs.length - 1);
        StringBuilder res = new StringBuilder();
        for (String s : strs) {
            res.append(s);
        }
        return res.toString();
    }

    private void quickSort(String[] strs, int l, int h) {
        if (l >= h) return;
        int i = l, j = h;
        String tmp = strs[i];
        while (i < j) {
            while (i < j && ((strs[j] + strs[l]).compareTo(strs[l] + strs[j]) >= 0)) {
                j--;
            }
            while (i < j && ((strs[i] + strs[l]).compareTo(strs[l] + strs[i]) <= 0)) {
                i++;
            }
            tmp = strs[i];
            strs[i] = strs[j];
            strs[j] = tmp;

        }
        strs[i] = strs[l];
        strs[l] = tmp;
        quickSort(strs, l, i - 1);
        quickSort(strs, i + 1, h);
    }
}
```