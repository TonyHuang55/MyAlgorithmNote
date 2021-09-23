### 剑指 Offer 50. 第一个只出现一次的字符

>在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。
***
>示例 1:  
>输入：s = "abaccdeff"  
>输出：'b'  

>示例 2:  
>输入：s = ""   
>输出：' '  
***
```java
class Solution {
    public char firstUniqChar(String s) {
        LinkedHashMap<Character, Boolean> map = new LinkedHashMap<>();
        for (char c : s.toCharArray()) {
            map.put(c, !map.containsKey(c));
        }
        for (Map.Entry<Character, Boolean> entry : map.entrySet()) {
            if (entry.getValue()) {
                return entry.getKey();
            }
        }
        return ' ';
    }
}
```
