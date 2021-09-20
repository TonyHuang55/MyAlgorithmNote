### 剑指 Offer 30. 包含min函数的栈

>定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。
***
>示例:
>
>MinStack minStack = new MinStack();
>minStack.push(-2);
>minStack.push(0);
>minStack.push(-3);
>minStack.min();   --> 返回 -3.
>minStack.pop();
>minStack.top();      --> 返回 0.
>minStack.min();   --> 返回 -2.
***
```java
class MinStack {

    Deque<Integer> stack;
    Deque<Integer> minStack;

    /**
     * initialize your data structure here.
     */
    public MinStack() {
        stack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
    }

    public void push(int x) {
        stack.push(x);
        // min 栈中没有数字或当前的最小值比当前的数字要大时
        // 压入当前数字
        if (minStack.isEmpty() || minStack.peek() >= x) {
            minStack.push(x);
        }
    }

    public void pop() {
        // 如果从主栈中弹出的数字是最小值，那么最小值栈也要弹出更新
        if (stack.pop().equals(minStack.peek())) {
            minStack.pop();
        }
    }

    public int top() {
        return stack.peek();
    }

    public int min() {
        return minStack.peek();
    }
}
```