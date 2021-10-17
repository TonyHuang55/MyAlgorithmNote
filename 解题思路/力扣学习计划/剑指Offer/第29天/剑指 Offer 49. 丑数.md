### 剑指 Offer 49. 丑数

>我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。
***
```java
class Solution {
    public int nthUglyNumber(int n) {
        int two = 0, three = 0, five = 0;
        int[] dp = new int[n];
        dp[0] = 1;
        for (int i = 1; i < n; i++) {
            int n2 = dp[two] * 2, n3 = dp[three] * 3, n5 = dp[five] * 5;
            dp[i] = Math.min(Math.min(n2, n3), n5);
            if (dp[i] == n2) two++;
            if (dp[i] == n3) three++;
            if (dp[i] == n5) five++;
        }
        return dp[n - 1];
    }
}
```