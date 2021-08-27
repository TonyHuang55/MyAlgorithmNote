### 剑指 Offer 58 - II. 左旋转字符串

>字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。
***
>示例 1：   
>输入: s = "abcdefg", k = 2   
>输出: "cdefgab"   

>示例 2：   
>输入: s = "lrloseumgh", k = 6   
>输出: "umghlrlose"   
***
```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        int len = s.length();
        StringBuilder sb = new StringBuilder(s);
        //反转前 n 个字符
        reverseString(sb, 0, n - 1);
        //反转其他字符
        reverseString(sb, n, len - 1);
        //反转全部
        reverseString(sb, 0, len - 1);
        return sb.toString();
    }

    public void reverseString(StringBuilder sb, int start, int end) {
        while (start < end) {
            char temp = sb.charAt(start);
            sb.setCharAt(start, sb.charAt(end));
            sb.setCharAt(end, temp);
            start++;
            end--;
        }
    }
}
```
