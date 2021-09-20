### 剑指 Offer 06. 从尾到头打印链表

>输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。
***
>示例 1：  
>输入：head = [1,3,2]  
>输出：[2,3,1]  
***
```java
class Solution {
    public int[] reversePrint(ListNode head) {
        Deque<ListNode> stack = new LinkedList<>();
        ListNode cur = head;
        while (cur != null) {
            stack.push(cur);
            cur = cur.next;
        }
        int[] ans = new int[stack.size()];
        int count = 0;
        while (!stack.isEmpty()) {
            ans[count++] = stack.pop().val;
        }
        return ans;
    }
}
```