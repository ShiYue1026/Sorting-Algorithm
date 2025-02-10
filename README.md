# 十大排序

## 基础

**十大排序分类**

![image.png](https://pic.leetcode-cn.com/1652691519-SVKUkM-image.png)



**什么是排序稳定性？**

稳定性是排序算法的一个重要性质，指的是<u>对于序列中的相同元素，如果排序之后它们的相对位置没有发生改变</u>，则称这个排序算法为**稳定排序**，反之为**不稳定排序**。

 

**是否是原地排序？**

原地排序就是指排序过程中不需要额外的辅助空间，只需要常数级别的额外空间，<u>直接操作原数组进行排序</u>。



## 1 选择排序

每次遍历数组，寻找数组中最小的元素，将其移动到前面；接着再在剩余的元素中遍历寻找，不断循环直到结尾。

![select.gif](https://pic.leetcode-cn.com/1652692082-UXzLDc-select.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {
        for(int i=0; i<nums.length - 1; i++){
            int min = nums[i];
            int minIdx = i;
            for(int j=i; j<nums.length; j++){
                if(nums[j] < min){
                    min = nums[j];
                    minIdx = j;
                }
            }
            // 交换i和minIdx位置上的元素
            if(i != minIdx){
                swap(nums, i, minIdx);
            }
        }
        return nums;
    }

    void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```



**稳定性**：

- 不稳定
  - 存在跨越交换。找到本轮次最小值之后，将其与本轮起始数字交换，此时若中间有与起始元素同值的元素，将打破稳定性。

**时间复杂度**

- $O(N^2)$
  - 两层for循环，第一轮比较n - 1次，最后一轮比较1次，总比较次数为 $\frac{n \times (n - 1)}{2} $

**空间复杂度**

- $O(1)$
  - 在原数组上进行操作

**优化**

- 双元选择排序
  - 在每一轮遍历中同时寻找最小值和最大值
  - 遍历次数减少一半，但每轮次的操作变多，因此该优化 **只能少量提升选择排序的速度** 



## 2 冒泡排序

每次遍历数组，比较相邻两个元素，如果左边比右边大，交换这两个元素，第一轮遍历能保证最大的元素到数组倒数第一个位置，第二轮遍历能保证第二大的元素到数组倒数第二个位置……

![bubble.gif](https://pic.leetcode-cn.com/1652691998-pJaTQD-bubble.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {
        for(int i=0; i<nums.length - 1; i++){
            for(int j=1; j<nums.length - i; j++){
                if(nums[j - 1] > nums[j]){
                    swap(nums, j - 1, j);
                }
            }
        }
        return nums;
    }

    void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

**稳定性**

- 稳定
  - 冒泡排序始终只交换相邻元素，比较对象大小相等时不交换，相对位置不变，故稳定。

**时间复杂度**

- $O(N^2)$
  - 两层for循环，第一轮比较n-1次，最后一轮比较1次，总比较次数为 $\frac{n \times (n - 1)}{2} $

**空间复杂度**

- $O(1)$
  - 在原数组上进行操作

**优化**

- 提前结束优化
  - 用一个全局布尔变量记录当前这一轮遍历是否发生交换行为，如果没发生说明当前数组已经是有序数组，最优情况下时间复杂度为$O(N)$



## 3 插入排序

从数组的第二个元素开始（插入对象元素），比较它与之前的元素，当插入对象元素小于比较对象元素时，继续往前比较，直到不小于**(≥)**比较对象，此时将插入元素插入到该次比较对象元素之后。重复这个插入过程直到最后一个元素。

![insert.gif](https://pic.leetcode-cn.com/1652692170-fPOTLH-insert.gif)

**实现**

```java
class Solution {
	public int[] sortArray(int[] nums) {
        if(nums.length < 2){
            return nums;
        }
        for(int i=1; i<nums.length; i++){
            int j = i - 1;
            int insertNum = nums[i];
            for(; j>=0; j--){
                if(insertNum < nums[j]){
                    nums[j + 1] = nums[j];  // 向后移动，腾出空位给插入元素
                } else{
                    break;  // 找到插入位置
                }
            }
            nums[j + 1] = insertNum;
        }
        return nums;
    }
}
```

**稳定性**

- 稳定
  - 对于大小相同的两个数字，插入和使得后来的数字靠右放置 （因为条件是 `≥` ），因此不会改变其相对位置。

**时间复杂度**

- $O(N^2)$
  - 两层for循环，第一轮比较n-1次，当原数组逆序时，移动次数为 $\frac{n \times (n - 1)}{2} $，时间复杂度为$O(N^2)$
  - 当原数组已基本有序时，接近线性复杂度$O(N)$。例如原数组已完全排序，则算法只需比较 n - 1 次。

**空间复杂度**

- $O(1)$
  - 在原数组上进行操作



## 4 希尔排序

希尔排序是基于插入排序的改进，基于以下事实

- 插入排序对 **排序程度较高的序列** 有较高的效率。
- 插入排序每次比较最多将数字移动一位，效率较低。

![img](https://img.jbzj.com/file_images/article/201912/2019122515223475.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {
        if(nums.length < 2){
            return nums;
        }
        int n = nums.length;
        for(int gap = n/2; gap > 0; gap = gap / 2){
            for(int start = 0; start < gap; start++){
                // 里面其实就是插入排序，只不过间隔从1变为gap
                for(int i = start + gap; i < n; i = i + gap){
                    int j = i - gap;
                    int insertNum = nums[i];
                    for(; j>=0; j = j - gap){
                        if(insertNum < nums[j]){
                            nums[j + gap] = nums[j];
                        } else{
                            break;
                        }
                    }
                    nums[j + gap] = insertNum;
                }
            }
        }
        return nums;
    }
}
```

**稳定性**

- 不稳定
  - gap大于1时，原本相同大小的元素可能在各自两个不同的子序列的排序中改变了相对位置。

**时间复杂度**

- **最坏情况**：$O(N^2)$（类似插入排序）

- **平均情况：**$O(N^{1.5})$（稍优于 O(n²)）

- **最好情况**：$O(Nlog_2N)$（接近最优）

**空间复杂度**

- $O(1)$
  - 在原数组上进行操作



## 5 快速排序

通过 选取基准值（pivot），将数组划分为 **小于基准值的部分、等于基准值的部分、大于基准值的部分**，就能确定基准值在有序数组中的位置，然后对左右两部分递归执行相同的操作，最终使整个数组有序

![quick.gif](https://pic.leetcode-cn.com/1652692926-vUyVEI-quick.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {        
        return quickSort(nums, 0, nums.length - 1);
    }

    int[] quickSort(int[] nums, int left, int right){
        if(left < right){
            int pivotIndex = partition(nums, left, right);
            quickSort(nums, left, pivotIndex - 1);
            quickSort(nums, pivotIndex + 1, right);
        }
        return nums;
    }

    int partition(int[] nums, int left, int right){
        // 随机选择pivot
        Random random = new Random();
        int idx = left + random.nextInt(right - left + 1);
        swap(nums, left, idx);
        int pivot = nums[left];
        while(left != right){
            // 从right开始
            while(right > left && nums[right] >= pivot){
                right--;
            }
            nums[left] = nums[right];

            while(left < right && nums[left] <= pivot){
                left++;
            }
            nums[right] = nums[left];
        }
        nums[left] = pivot;
        return left;
    }

    void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

**稳定性**

- 不稳定
  - 例：[7 2 4 4 8 9]
  - 7 为主轴元素，partition过后交换 7 和第二个 4 ，则两个 4 的位置关系发生变化

**时间复杂度**

- **最好情况**：$O(Nlog_2N)$（快速排序的递归树层级为$log_2N$）
- **最坏情况**：$O(N^2)$（快速排序的递归树层级为$N$，元素本来就有序且选第一个元素作为pivot）

**空间复杂度**

- 取决于递归的层数
- **最好情况**：$O(log_2N)$
- **最坏情况**：$O(N)$

**优化**

- 每次pivot为**随机元素**
- 每次pivot为**左中右三数大小居中者** 



## 6 归并排序



## 7 堆排序



## 8 计数排序



## 9 桶排序



## 10 基数排序





## 参考

https://leetcode.cn/circle/discuss/eBo9UB/

https://labuladong.online/algo/data-structure-basic/sort-basic/
