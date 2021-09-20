### 剑指 Offer 35. 复杂链表的复制

>请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。
***
>示例 1：  
>![示例1](e1.png)  
>输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]  
>输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]  

>示例 2：  
>![示例2](e2.png)  
>输入：head = [[1,1],[2,1]]  
>输出：[[1,1],[2,1]]  

>示例 3：  
>![示例3](e3.png)  
>输入：head = [[3,null],[3,0],[3,null]]  
>输出：[[3,null],[3,0],[3,null]]  

>示例 4：  
>输入：head = []  
>输出：[]  
>解释：给定的链表为空（空指针），因此返回 null。  
***
> 回溯 + 哈希表
```java
class Solution {
    HashMap<Node, Node> map = new HashMap<Node, Node>();

    public Node copyRandomList(Node head) {
        if (head == null) return null;

        if (!map.containsKey(head)) {
            Node newHead = new Node(head.val);
            map.put(head, newHead);
            newHead.next = copyRandomList(head.next);
            newHead.random = copyRandomList(head.random);
        }
        return map.get(head);
    }
}
```
***
>迭代 + 节点拆分
```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;

        // 1、将链表中每个节点复制一份放在他的后面
        // A -> B -> C   ==>    A -> A' -> B -> B' -> C -> C'
        for (Node node = head; node != null; node = node.next.next) {
            Node newNode = new Node(node.val);
            newNode.next = node.next;
            node.next = newNode;
        }

        // 2、用复制的节点进行随机节点连接的赋值
        for (Node node = head; node != null; node = node.next.next) {
            Node newNode = node.next;
            newNode.random = (node.random != null) ? node.random.next : null;
        }

        Node resHead = head.next;
        // 3、分离
        for (Node node = head; node != null; node = node.next) {
            Node newNode = node.next;
            node.next = node.next.next;
            newNode.next = (newNode.next != null) ? newNode.next.next : null;
        }
        return resHead;
    }
}
```
