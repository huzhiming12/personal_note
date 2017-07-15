# 包含min函数的栈

> 题目：定义栈的数据结构，请在该类型中实现一个能够得到栈最小元素的min函数。

思路：定义两个栈，第一个栈values存放元素，第二个栈min存放当前最小的元素，例如一次添加元素{4,6,3,7,8,2,6,2,1,3,5}

values：4,6,3,7,8,2,6,2,1,3,5

​     min:  4,#,3,#,#,2,#,2,1,#,#     其#表示加入元素的时候min栈不操作

```Java
import java.util.Stack;

public class Solution {
    
    private Stack<Integer>values = new Stack<>();
    private Stack<Integer>min = new Stack<>();
    
    public void push(int node) 
    {
       if(!min.isEmpty())
       {
           int temp = min.peek();
           if(node<=temp)
               min.push(node);
       }else
           min.push(node);
        
       values.push(node);
    }
    
    public void pop() {
        if(!values.isEmpty())
        {
        	int temp = values.pop();
            if(min.peek()==temp)
                min.pop();
        }
    }
    
    public int top() {
        if(values.isEmpty())
            return values.peek();
        else
            return 0;
    }
    
    public int min() {
        return min.peek();
    }
}
```