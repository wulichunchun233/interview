# 牛客网刷题列表

## 2018校招真题

### 1. 关灯游戏

##### 题目描述：

在Alice生日的那天，Bob送给了她n个灯泡。他们决定用这些灯泡玩一个游戏：他们把这些灯泡从左往右排成一行，在初始时，有些灯泡是点亮的，有些灯泡是熄灭的。接下来，他们轮流进行操作，Alice首先操作。在每一次操作中，轮到操作的人需要选择一个点亮的灯泡，然后把它以及它右边的所有灯泡的状态进行一次改变，即把点亮的灯泡熄灭，把熄灭的灯泡点亮。如果在某一个人操作完之后，所有的灯泡都变成了熄灭状态，那么那个人就赢得了游戏。Alice和Bob都想赢得游戏，在他们都足够聪明的情况下，最后谁会赢呢？

##### 输入描述:

第一行包含一个整数 ，表示灯泡的个数。1 ≤ n ≤ 105
第二行包含 个 0 或 1，表示初始时灯泡的状态，0 表示熄灭，1 表示点亮。

##### 输出描述:

如果最后Alice能赢，输出Alice，或则输出Bob。

示例1：

输入

> 3
>
> 0 1 1

输出

> Alice

示例2：

输入
> 5
>
> 1 1 1 0 0

输出

> Bob

##### 代码

```java
import java.util.Scanner;

public class Main{
    public static int a[] = new int[100000];
    public static int m,n;
    public static void main(String[] args){
        // 获取输入数据
        Scanner scanner = new Scanner(System.in);
        n = scanner.nextInt();
        for(int i=0;i<n;i++){
            a[i] = scanner.nextInt();
        }
        scanner.close();
        // 开始对数据进行处理
        m=0;
        boolean flag = true;
        while(flag){
            for(int i=0;i<n;i++){
                // 对从第一个1开始之后的所有数字进行反转
                if(a[i]==1){
                    for(int j=i;j<n;j++){
                        a[j] = a[j]^1;
                    }
                    break;
                }
            }
            flag = is_success();
        }
    }
    /**
    判断当前数组是否满足结束条件
    */
    public static boolean is_success(){
        for(int i=0;i<n;i++){
                if(a[i]==1){
                    m++;
                    return true;
                }
        }
        // 满足结束条件的情况下输出是谁获胜了
        if(m%2==0){
            System.out.println("Alice");
        }else{
            System.out.println("Bob");
        }
        return false;
    }
}
```