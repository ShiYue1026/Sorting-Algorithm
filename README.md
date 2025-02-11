# 十大排序

## 基础


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

- **最好情况**：$O(NlogN)$（快速排序的递归树层级为$log_2N$）
- **最坏情况**：$O(N^2)$（快速排序的递归树层级为$N$，元素本来就有序且选第一个元素作为pivot）

**空间复杂度**

- 取决于递归的层数
- **最好情况**：$O(logN)$
- **最坏情况**：$O(N)$

**优化**

- 每次pivot为**随机元素**
- 每次pivot为**左中右三数大小居中者** 



## 6 归并排序

归并排序将原待排数组递归或迭代地分为左右两半，直到数组长度为1，然后合并（merge）左右数组，在合并中完成排序。合并过程采用非原地合并方法，即依次比较两部分已排序数组，将比较结果依次写入新空间中。

![merge.gif](https://pic.leetcode-cn.com/1652692297-eViXmh-merge.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {        
        mergeSort(nums, 0, nums.length - 1);
        return nums;
    }

    void mergeSort(int[] nums, int left, int right) {
        if(left >= right){
            return;
        }
        int mid = (left + right) / 2;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }

    void merge(int[] nums, int left, int mid, int right) {
        int len = right - left + 1;
        int[] temp = new int[len];
        int l = left;
        int r = mid + 1;
        int idx = 0;
        while(l <= mid && r <= right){
            if(nums[l] <= nums[r]){
                temp[idx] = nums[l];
                l++;
            } else{
                temp[idx] = nums[r];
                r++;
            }
            idx++;
        }
        while(l <= mid) {
            temp[idx] = nums[l];
            idx++;
            l++;
        }
        while(r <= right) {
            temp[idx] = nums[r];
            idx++;
            r++;
        }
        System.arraycopy(temp, 0, nums, left, len);
    }
}
```

**稳定性**

- 稳定
  - 合并时的此判断中的等号 `if(left[l_next] <= right[r_next])`，保证了出现相等元素时，居左的元素总会被放在左侧，即排序是稳定的。

**时间复杂度**

- $O(NlogN)$

**空间复杂度**

- $O(N)$

  - 递归深度为$logN$

  - 递归过程中需要一个长度为n的临时数组保存中间结果

    

## 7 堆排序

首先建立大顶堆，从最后一个非叶节点开始依次向下调整；建完堆之后，进行排序，每轮堆顶换到最后，向下调整新的堆顶，这一步总共需要n-1轮。

**建堆**：

![heapify.gif](https://pic.leetcode-cn.com/1652693123-umfUhL-heapify.gif)

**排序**：

![siftDown.gif](https://pic.leetcode-cn.com/1652693170-JMfGtM-siftDown.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {        
        int n = nums.length;

        // 1. 建堆
        for(int i = n / 2 - 1; i >= 0; i--){  // n / 2 - 1是最后一个非叶子节点
            heapify(nums, n, i);
        }

        // 2. 排序
        for(int i = n - 1; i > 0; i--){
            swap(nums, 0, i);  // 交换最大值到数组末尾
            heapify(nums, i, 0);  // 重新调整堆（排除已排序部分）
        }

        return nums;
    }

    void heapify(int[] nums, int heapsize, int i) {
        int largest = i;
        int left = i * 2 + 1;  // 左子节点对应数组下标
        int right = i * 2 + 2;  // 右子节点对应数组下标
        
        if(left < heapsize && nums[left] > nums[largest]){
            largest = left;
        }

        if(right < heapsize && nums[right] > nums[largest]){
            largest = right;
        }

        if(largest != i){
            swap(nums, largest, i);
            heapify(nums, heapsize, largest);
        }
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

  - 因为排序时每次需要把根节点交换到最后去，会破坏相对顺序

    ![image.png](https://pic.leetcode-cn.com/1652693077-zEkBlq-image.png)

**时间复杂度**

- $O(NlogN)$

**空间复杂度**

- $O(1)$：原地对排序算法中只有常数项变量



## 8 计数排序

计数排序是一种非比较排序算法，通过统计每个元素出现的次数，并利用前缀和计算元素在排序后数组中的位置，最终将元素按顺序填充回原数组。

![count.gif](https://pic.leetcode-cn.com/1652693288-wsXoKv-count.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {        
        // 首先找到最大值最小值
        int min = nums[0];
        int max = nums[0];

        for(int i=0; i<nums.length; i++){
            if(nums[i] < min){
                min = nums[i];
            }
            if(nums[i] > max){
                max = nums[i];
            }
        }

        // 构建count数组统计每个元素出现的次数
        int[] count = new int[max - min + 1];

        for(int i=0; i<nums.length; i++){
            count[nums[i] - min]++; 
        }

        // 构建前缀和数组
        for(int i=1; i<count.length; i++){
            count[i] += count[i - 1];
        }

        // 逆序遍历原数组填充输出数组保证稳定性
        int[] output = new int[nums.length];
        for(int i=nums.length - 1; i>=0; i--){
            output[count[nums[i] - min] - 1] = nums[i];
            count[nums[i] - min]--;
        }
        return output;
    }
}
```

**稳定性**

- 取决于最后一步构建输出数组时是否逆序遍历原数组，逆序的话就是稳定的，反之，不稳定。

**时间复杂度**

- $O(N + K)$
  - N是要排序数组的长度，K是数组中最大值与最小值的距离

**空间复杂度**

- $O(N + K)$
  - N是输出数组的长度，K是count数组的长度



## 9 桶排序

桶排序将原数组划分到称为 **「桶」** 的多个区间中，然后对每个桶单独进行排序，之后再按桶序和桶内序输出结果。适合于分布较均匀的数据。

![img](https://i-blog.csdnimg.cn/blog_migrate/585d6bd0bf3da609c5b4288f070dec9c.gif#pic_center)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {        
        // 首先找到最大值最小值
        int min = nums[0];
        int max = nums[0];

        for(int i=0; i<nums.length; i++){
            if(nums[i] < min){
                min = nums[i];
            }
            if(nums[i] > max){
                max = nums[i];
            }
        }

        // 计算桶的数量并创建桶
        int bucketSize = 3;
        int bucketCount = (int) Math.ceil((max - min) / bucketSize) + 1;
        List<List<Integer>> buckets = new ArrayList<>();
        for(int i=0; i<bucketCount; i++){
            buckets.add(new ArrayList<>());
        }

        // 将元素分配到桶中
        for(int i=0; i<nums.length; i++){
            int index = (nums[i] - min) / bucketSize;
            buckets.get(index).add(nums[i]); 
        }

        // 对每个桶内进行排序（使用内置排序）
        for (List<Integer> bucket : buckets) {
            Collections.sort(bucket);
        }

        // 按顺序合并桶内元素
        int index = 0;
        for (List<Integer> bucket : buckets) {
            for (int num : bucket) {
                nums[index++] = num;
            }
        }

        return nums;
    }
}
```

**稳定性**

- 取决于桶内排序方法的稳定性

**时间复杂度**

- 找最大最小值和分配桶均耗费$O(N)$，之后的复杂度取决于每个桶内的排序算法复杂度之和。假设有K个桶，且数据分布均匀若采用 $O(NlogN) $的排序算法，总排序时间复杂度为 $O(K(N/K)log(N/K))$，即$O(Nlog(N/K))$

**空间复杂度**

- $O(N + K)$
  - 桶数组 buckets[]：$O(K)$
  - 所有桶共存储N个元素：$O(N)$



## 10 基数排序

非比较排序，「基」指的是数的位，例如十进制数 123，共有百十个位，共 3 「位」。基数排序 按数字的位进行循环，每一轮操作都是对当前位 (基数) 的计数排序，使得输出到输出数组后所有数字在截止到当前位上 (即去掉未考察的位后) 是排序状态，考察完最大位后完成排序。

![radix.gif](https://pic.leetcode-cn.com/1652693343-bsRGEO-radix.gif)

**实现**

```java
class Solution {
    public int[] sortArray(int[] nums) {        
        // 首先找到最大值
        int max = nums[0];

        for(int i=0; i<nums.length; i++){
            if(nums[i] > max){
                max = nums[i];
            }
        }

        int exp = 1;
        while(max / exp > 0){
            sortByDigit(nums, exp);
            exp = exp * 10;
        }

        return nums;
    }

    void sortByDigit(int[] nums, int exp){
        int[] output = new int[nums.length];
        int[] count = new int[10];

        for(int i=0; i<nums.length; i++){
            int index = (nums[i] / exp) % 10;
            count[index]++;
        }

        for(int i=1; i<count.length; i++){
            count[i] += count[i - 1]; 
        }

        for(int i=nums.length - 1; i>=0; i--){
            int index = (nums[i] / exp) % 10;
            output[count[index] - 1] = nums[i];
            count[index]--;
        }

        System.arraycopy(output, 0, nums, 0, nums.length);
    }
}
```

**稳定性**

- 取决于构建输出数组时是否逆序遍历原数组，逆序的话就是稳定的，反之，不稳定。

**时间复杂度**

- $O(d \times (N + K))$
  - d是最大值的总位数
  - N是待排序数组的长度
  - K是进制基数（通常 K=10，即 0~9）

**空间复杂度**

- $O(N + K)$
  - 输出数组`output[]`：$O(N)$
  - 计数数组 `count[]`：$O(K)$



## 参考

https://leetcode.cn/circle/discuss/eBo9UB/
