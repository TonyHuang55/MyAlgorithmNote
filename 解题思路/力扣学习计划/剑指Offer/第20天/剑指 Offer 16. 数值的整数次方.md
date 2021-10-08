### 剑指 Offer 16. 数值的整数次方

>实现 pow(x, n) ，即计算 x 的 n 次幂函数（即，xn）。不得使用库函数，同时不需要考虑大数问题。
***
```java
class Solution {
    public double myPow(double x, int n) {
        if (x == 0) return 0;
        long b = n;
        double res = 1;
        if (b < 0) {
            b *= -1;
            x = 1 / x;
        }
        while (b >= 1) {
            if (b % 2 == 1) res *= x;
            x *= x;
            b /= 2;
        }
        return res;
    }
}
```