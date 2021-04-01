## 数组中重复的数字

思路一：Hash 表，遍历数组，添加到 set 集合中，添加失败则返回。

思路二：因为范围在 0 到 n-1，根据数组下标，找到下标和元素值不相等的。

## 二维数组中的查找

![image-20210328142113841](media/image-20210328142113841.png)

思路：从右上角开始寻找，如果目标值比当前值小，则往左走（因为当前列都比当前值大），如果目标值比当前值大，则往下走

## 替换空格

将空格变成`%20`

思路：

## 重建二叉树

## 二叉树的下一个节点

给定一颗二叉树的其中一个节点，请找出中序遍历的下一个节点

思路：一个节点，有右子树，返回的一定是右子树最左边的节点。没有右子树，返回的是父亲节点。

```JAVA
public TreeNode inorderSuccess(TreeNode p){
    //有右子树，返回的一定是右子树最左边的节点。
    if(p.right !=null){
        p = p.right;
        while(p.left!=null) p= p.left;
        return p;
    }
    //没有右子树，返回父亲节点
    while(p.father!=null){
        if(p==p.father.left)
            return p.father;
        p = p.father;
    }
    return null;
}
```

## 旋转数组的最小数字

如：将【1,2,3,4,5】翻转为【3,4,5,1,2】，找到旋转后的数组的最小值。

思路：临界点，二分

## 矩阵中的路径

回溯算法，深度优先 dfs

## 机器人的运动范围

略，上个文件有

## 剪绳子

![image-20210328144802775](media/image-20210328144802775.png)

思路：最值，dp

我们想要求长度为`n`的绳子剪掉后的最大乘积，可以从前面比`n`小的绳子转移而来。设`dp[i] ` 为长度为 i 的最大乘积

从长度为 2 开始剪（因为剪 1 乘积没变化）剪了第一段后，剩下`(i - j)`长度可以剪也可以不剪。如果不剪的话长度乘积即为`j * (i - j)`；如果剪的话长度乘积即为`j * dp[i - j]`。取两者最大值`max(j * (i - j), j * dp[i - j])`

```java
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];
        dp[2] = 1;
        for(int i = 3; i < n + 1; i++){
            for(int j = 2; j < i; j++){
                dp[i] = Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j]));
            }
        }
        return dp[n];
    }
```

思路 2：贪心，根据数学求最大值证明，(证明连接)(https://leetcode-cn.com/problems/jian-sheng-zi-lcof/solution/mian-shi-ti-14-i-jian-sheng-zi-tan-xin-si-xiang-by)尽可能分成长度为3的小段，乘积最大。

步骤如下：

如果 n == 2，返回 1，如果 n == 3，返回 2，两个可以合并成 n 小于 4 的时候返回 n - 1

如果 n == 4，返回 4

如果 n > 4，分成尽可能多的长度为 3 的小段，每次循环长度 n 减去 3，乘积 res 乘以 3；最后返回时乘以小于等于 4 的最后一小段

```java
    public int cuttingRope(int n) {
        if(n < 4){
            return n - 1;
        }
        int res = 1;
        while(n > 4){
            res *= 3;
            n -= 3;
        }
        return res * n;
    }
```
