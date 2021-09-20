### 剑指 Offer 24. 反转链表

>定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。
***
>示例:  
>输入: 1->2->3->4->5->NULL  
>输出: 5->4->3->2->1->NULL  
***
>迭代
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            // 当前节点的下一个用 next 存储
            ListNode next = curr.next;
            // 当前节点指向前一个
            curr.next = prev;
            // 更新前一串节点和当前节点
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```
***
>递归
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        return reverseHelper(head, null);
    }

    private ListNode reverseHelper(ListNode cur, ListNode pre) {
        // 终止条件
        if (cur == null) return pre;
        // 递归后继节点
        ListNode res = reverseHelper(cur.next, cur);
        // 修改节点指向
        cur.next = pre;
        return res;
    }
}
```