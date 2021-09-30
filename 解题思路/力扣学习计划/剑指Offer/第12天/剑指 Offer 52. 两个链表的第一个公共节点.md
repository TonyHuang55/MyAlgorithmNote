### 剑指 Offer 52. 两个链表的第一个公共节点

>输入两个链表，找出它们的第一个公共节点。
>
>注意：
>* 如果两个链表没有交点，返回 null.
>* 在返回结果后，两个链表仍须保持原有的结构。
>* 可假定整个链表结构中没有循环。
>* 程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存
***
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode A = headA, B = headB;
        while (A != B) {
            A = A != null ? A.next : headB;
            B = B != null ? B.next : headA;
        }
        return A;
    }
}
```

