### 剑指 Offer 27. 二叉树的镜像

>请完成一个函数，输入一个二叉树，该函数输出它的镜像。
***
>递归
```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if (root == null) return null;
        TreeNode left = mirrorTree(root.left);
        TreeNode right = mirrorTree(root.right);
        root.left = right;
        root.right = left;
        return root;
    }
}
```
***
>辅助栈
```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if (root == null) return null;
        Deque<TreeNode> stack = new LinkedList<>();
        stack.add(root);
        while (!stack.isEmpty()) {
            TreeNode cur = stack.poll();
            if (cur.left != null) stack.add(cur.left);
            if (cur.right != null) stack.add(cur.right);
            TreeNode tmp = cur.left;
            cur.left = cur.right;
            cur.right = tmp;
        }
        return root;
    }
}
```