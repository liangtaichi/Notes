# 剑指offer 13 机器人的运动范围

## 题目描述：

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

## 示例输入输出：

```
输入：m = 2, n = 3, k = 1
输出：3
```

```
输入：m = 3, n = 1, k = 0
输出：1
```

## 思路

### 1. DFS回溯

* 暴力遍历机器人所有路径。DFS通过递归，先朝一个方向走到尽头，再回溯到上一个节点，向另一个方向前行。

* 算法解析：

  * **参数：**行列参数xy、坐标和sumx与sumy。
  * **终止条件：**
    * 行列越界
    * sumx+sumy大于目标值k
    * 元素已经访问过
  * **递推过程中要做的事情：**
    * 标记当前单元（存入标志数组array）
    * 探索下一单元格（x+1或y+1）
  * **返回值：**1+向右搜索可达总数+向下搜索可达总数

* 复杂度分析：

  * 时间复杂度：最差情况下，机器人需要遍历所有单元格，即走m*n格，所以为O（MN）
  * 空间复杂度：最差情况下，array需要存所有单元格，array\[ m\] \[ n ],空间复杂度为O（MN）。

* 代码：

  * 核心代码模式：

  * ```java
    class Solution{
        /**
        *@m:矩阵列数
        *@n:矩阵行数
        *@k:目标值
        **/
        int m,n,k;
        /**
        *@array:标志数组
        */
        boolean[][] array;
        public int movingCount(int m,int n, int k){
            this.m = m;
            this.n = n;
            this.k = k;
            this.array = new boolean[m][n];
            return dfs(0,0,0,0);
        }
        private int dfs(int x, int y, int sumx, int sumy){
            //行列越界或sumx+sumy大于目标值k或元素已经访问过，返回0
            if(x>=m || y>=n 
              || k < sumx+sumy || array[x][y]){
                return 0;
            }
            //标记数组
            array[x][y] = true;
            //向下与向右继续遍历
            return 1 + dfs(x+1, y, overFlow(sumx), sumy) 
                + dfs(x, y, sumx, overFlow(sumy));
        }
        //target+1小于10，则target-1；大于10，则从头计算
        //19,20数位和10，2；1，2数位和12
        private int overFlow(int target){
            if((target+1)%10 != 0){
                return target+1;
            }else{
                return target-8;
            }
        }
    }
    ```

    

### 2.BFS迭代

* 仍然是暴力遍历，不过与dfs不同，BFS更倾向于摊开。利用队列实现。

* 算法解析：

  * **初始化：**初始点0，0加入队列。
  * **终止条件：**队列为空。
  * **迭代过程中要做的事情：**
    * 单元格出队（队首的单元格弹出）
    * 判断是否跳过
      * 行列越界
      * sumx+sumy大于目标值k
      * 元素已经访问过
    * 标记单元格（存入数组array中）
    * 单元格入队

* **复杂度分析：**见dfs。

* 代码：

  * 核心代码模式：

  * ```java
    class Solution {
        public int movingCount(int m, int n, int k) {
        	boolean[][] array = new boolean[m][n];
            int result = 0;
            Queue<int[]> queue = new LinkedList<>();
            //添加第一个点，数据分别表示x,y,sumx,sumy
            queue.add(new int[]{0,0,0,0});
            while(queue.size() > 0){
                int[] target = queue.poll();
                int x = target[0], y = target[1],
                sumx = target[2], sumy = target[3];
                //不符合条件直接跳过
                if(x >= m || y >=n 
                   || sumx+sumy > k || array[x][y]){
                    continue;
                }
                //修改标志数组
                array[x][y] = true;
                //每修改一次数组，表示多了一个可达位置
                result++;
                //把隔壁两个格子添加进队列
                queue.add(new int[] {x+1, y, overFlow(sumx), sumy});
                queue.add(new int[] {x, y+1, sumx, overFlow(sumy)});
            }
            return result;
        }
        private int overFlow(int target){
            if((target+1)%10 != 0){
                return target+1;
            }else{
                return target-8;
            }
    }
    ```

    