### 剑指 Offer 55 - I. 二叉树的深度

> 输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。
***
```java
class Solution {
    public int maxDepth(TreeNode root) {
        Queue<TreeNode> que = new LinkedList<>();
        if (root != null) que.offer(root);
        int depth = 0;
        while (!que.isEmpty()) {
            for (int i = que.size(); i > 0; i--) {
                TreeNode cur = que.poll();
                if (cur.left != null) que.offer(cur.left);
                if (cur.right != null) que.offer(cur.right);

            }
            depth++;
        }
        return depth;
    }
}
```