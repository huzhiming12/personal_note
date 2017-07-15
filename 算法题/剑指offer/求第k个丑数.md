# 求第k个丑数

**题目**：把只包含素因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

```Java
import java.util.*;

public class Solution {
    public int GetUglyNumber_Solution(int index) {
    	if(index==0)
            return 0;
        
        int []uglyNum=new int[index];
        int id2=0;
        int id3=0;
        int id5=0;
        uglyNum[0]=1;
        int counter=1;
        while(counter<index){
            
            int num = getMinNum(uglyNum[id2]*2,uglyNum[id3]*3,uglyNum[id5]*5);
            if(num==uglyNum[id2]*2)
                id2++;
            if(num==uglyNum[id3]*3)
                id3++;
            if(num==uglyNum[id5]*5)
                id5++;
            uglyNum[counter]=num;
            counter++;
        }
        return uglyNum[index-1];
    
    }
    //求三个数中最小的数
    public int getMinNum(int a,int b,int c){
        int temp = a>b?b:a;
        return temp>c?c:temp;
    }
       
}
```