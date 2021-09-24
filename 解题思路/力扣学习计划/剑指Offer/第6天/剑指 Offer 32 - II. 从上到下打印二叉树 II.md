### 剑指 Offer 32 - II. 从上到下打印二叉树 II

>从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。
***
```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        if (root != null) {
            stack.add(root);
        }
        while (!stack.isEmpty()) {
            int len = stack.size();
            List<Integer> tmp = new ArrayList<>();
            for (int i = 0; i < len; i++) {
                TreeNode cur = stack.poll();
                tmp.add(cur.val);
                if (cur.left != null) {
                    stack.add(cur.left);
                }
                if (cur.right != null) {
                    stack.add(cur.right);
                }
            }
            res.add(tmp);
        }
        return res;
    }
}
```