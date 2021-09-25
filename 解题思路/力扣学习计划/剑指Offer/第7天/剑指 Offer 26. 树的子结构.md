### 剑指 Offer 26. 树的子结构

>输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)
>
>B是A的子结构， 即 A中有出现和B相同的结构和节点值。
***
```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        // 特殊处理，如果 A 或 B 任意一个为 null 都返回 false
        // 根据先序遍历的顺序判断 B 是 A 的子树
        return (A != null && B != null) && (recur(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }

    private boolean recur(TreeNode A, TreeNode B) {
        // 如果 B 为 null，说明 B 已经匹配完成，返回 true
        if (B == null) return true;
        // 如果 A 为 null ，说明已经越过 A 叶子节点，返回 false
        // 如果 A 和 B 值不同，说明匹配失败，返回 false
        if (A == null || A.val != B.val) return false;
        // 判断 A 和 B 的左右孩子是否相等
        return recur(A.left, B.left) && recur(A.right, B.right);
    }
}
```