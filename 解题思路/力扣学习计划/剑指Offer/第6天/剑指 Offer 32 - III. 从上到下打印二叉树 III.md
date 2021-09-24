### 剑指 Offer 32 - III. 从上到下打印二叉树 III

>请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。
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

            List<Integer> tmp = new ArrayList<>();
            for (int i = stack.size(); i > 0; i--) {
                TreeNode cur = stack.poll();
                tmp.add(cur.val);
                if (cur.left != null) {
                    stack.add(cur.left);
                }
                if (cur.right != null) {
                    stack.add(cur.right);
                }
            }
            if (res.size() % 2 == 1) Collections.reverse(tmp);
            res.add(tmp);
        }
        return res;
    }
}
```