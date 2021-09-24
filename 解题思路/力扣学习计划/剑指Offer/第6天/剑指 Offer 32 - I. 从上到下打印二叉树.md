### 剑指 Offer 32 - I. 从上到下打印二叉树

>从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。
***
```java
class Solution {
    public int[] levelOrder(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if (root == null) {
            return new int[0];
        }
        Deque<TreeNode> stack = new LinkedList<>();
        stack.add(root);
        while (!stack.isEmpty()) {
            TreeNode cur = stack.poll();
            ans.add(cur.val);
            if (cur.left != null) {
                stack.add(cur.left);
            }
            if (cur.right != null) {
                stack.add(cur.right);
            }
        }
        int[] res = new int[ans.size()];
        for (int i = 0; i < ans.size(); i++) {
            res[i] = ans.get(i);
        }
        return res;
    }
}
```