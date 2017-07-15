# 二叉搜索树中第k个结点

#### 题目：

给定一颗二叉搜索树，请找出其中的第k个结点。例如， 5 / \ 3 7 /\ /\ 2 4 6 8 中，按结点数值大小顺序第三个结点的值为4。

解析：二叉搜索树的中序遍历可以得出一个从小到大的序列，进行中序遍历即可得到第K个结点，2、3、4、5、6、7、8

```Java
/*
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    int num=0; //全局计数器
    TreeNode KthNode(TreeNode pRoot, int k)
    {
        if(pRoot==null || k<0)
            return null;
        //遍历左子树
        if(pRoot.left!=null)
        {
            TreeNode node = KthNode(pRoot.left,k);
            if(node!=null)
                return node;
        }
      
        num++;
        if(num==k)
            return pRoot;
        //遍历右子树
        if(pRoot.right!=null)
        {
            TreeNode node = KthNode(pRoot.right,k);
            if(node!=null)
                return node;
        }
        return null;
    }
}
```