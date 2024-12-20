
# 二分查找的区间到底是开还是闭？



> 在这两个月的时间里，我似乎没有产出任何的有关知识点的文章，大多数都是题解相关的内容。以至于许多人觉得 Macw07 “失踪”了。本文是我来到北美之后的第一篇知识点文章，请大家多多关照。


这次不讲难的知识点了，讲一个大家都熟悉的，但又非常令人抓毛的算法：**二分查找和二分答案算法**。


### 引言 Introduction


**注意**：本文仅针对了解过二分查找基本算法原理的用户群体，若您从未接触过或了解过该算法，请先学习基础的二分查找算法。


二分查找算法是大家一个再熟悉不过的算法了，二分查找算法可以在一个 **有序数列** 中高效地查找某个或多个特定的目标值。一般来说，二分查找的时间复杂度在 O(log2⁡N)) 级别。二分算法非常适合在大数据集上实现快速查询。与此同时，除了基本的二分查找算法，它的许多变种也被广泛应用于各种场景，比如求最大值、最小值，甚至在复杂的数据结构中优化数据的查找性能。


许多同学肯定在学习完基本的二分查找后一直有一个疑问：我到底该如何设置 L 和 R 的区间闭合状态？什么时候需要输出 L 或 R，为什么有时候还需要 \+1？Mid 到底保存的是什么东西？etc.


事实上，**区间开闭的变量定义** 确实是一个核心且容易混淆的问题，在 CSP 考试中也常考此知识点，因此本文将重点围绕区间开闭的变量定义来展开。


### 二分查找的基本原理 Basic Principles of Binary Search


在深入讨论区间开闭之前，有必要回顾一下二分查找的基本原理。二分查找通过反复将搜索区间分成两半，逐步缩小目标值所在的范围，直到找到目标值或确定其不存在。具体步骤如下：


1. **初始化**：设定搜索区间的左右边界 L 和 R。
2. **计算中点**：计算中点 M\=L\+R−L2。
3. 比较


：将目标值与中点元素进行比较。


	* 若相等，返回中点位置。
	* 若目标值小于中点元素，缩小搜索区间至左半部分。
	* 若目标值大于中点元素，缩小搜索区间至右半部分。
4. **重复**：重复上述步骤，直到找到目标值或搜索区间为空。


### 开区间/闭区间 Open Interval/Closed Interval


在文章开始，先了解一下区间的开闭性。


#### 开区间


定义：开区间表示区间的端点 **不包含在区间内**，用小括号 () 表示。


示例：(2,5) 表示所有介于 2 和 5 之间的数，但不包含数字 2 和 5。


#### 闭区间


定义：开区间表示区间的端点 **包含在区间内**，用方括号 \[] 表示。


示例：\[2,5] 表示所有介于 2 和 5 之间的数，而且包含数字 2 和 5。


#### 半开区间/半闭区间


定义：半开区间或半闭区间表示区间的一个端点包含在内，另一个端点不包含在内。


示例：(2,5] 表示所有介于 2 和 5 之间的数，且包含数字 5，但不包含数字 2。




| 区间类型 | 表示方式 | 是否包含左端点 a | 是否包含右端点 b |
| --- | --- | --- | --- |
| 开区间 | (a,b) | 否 | 否 |
| 闭区间 | \[a,b] | 是 | 是 |
| 左开右闭 | (a,b] | 否 | 是 |
| 左闭右开 | \[a,b) | 是 | 否 |


### 区间开闭的类型 Interval Categories


在实现二分查找的时候，区间的定义是最常见的一个问题，你可能会看到过以下不同的区间开闭性的定义：


1. 左开右开 (left,right)
2. 左闭右闭 \[left,right]
3. 左开右闭 (left,right]
4. 左闭右开 \[left,right)


通常来说，我们一般会选择【左闭右开】或者【左闭右闭】的区间定义，所以本文也就着重围绕这两个部分讲解。但对于不同的定义区间，如果稍有不慎，就容易使代码进入 **死循环**。


#### 左闭右闭区间


**定义**：搜索区间包括 `left` 和 `right`，即 `left` 和 `right` 都可能是目标值。


**退出条件**：`left > right`，表示搜索区间为空。


左闭右闭区间的二分查找的常见写法如下：



```
while (left <= right) { // 注意是 <=
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) {
        return mid;
    } else if (nums[mid] < target) {
        left = mid + 1; // [mid+1, right]
    } else {
        right = mid - 1; // [left, mid-1]
    }
}

```

#### 左闭右开区间


**定义**：搜索区间包括 `left` 但不包括 `right`，即目标值可能是 `left`，但不可能是 `right`。


**退出条件**：当 `left == right` 时，表示搜索区间为空。


左闭右开区间的二分查找的常见写法如下：



```
while (left < right) { // 注意是 <
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) {
        return mid;
    } else if (nums[mid] < target) {
        left = mid + 1; // [mid+1, right)
    } else {
        right = mid; // [left, mid)
    }
}

```

### 两种区间的迭代过程中的差异 Differences During Iterating


#### `left` 的更新：


* **左闭右闭**：`left = mid + 1`，因为 `mid` 已经被检查过了，`mid+1` 开始的新区间仍是闭区间。
* **左闭右开**：`left = mid + 1`，保持 `right` 的开区间性质。


#### `right` 的更新：


* **左闭右闭**：`right = mid - 1`，因为 `mid` 已经被检查过了，`mid-1` 保证了闭区间不重复。
* **左闭右开**：`right = mid`，将 `mid` 排除，保证开区间不包含 `right`。


#### 退出条件：


* **左闭右闭**：循环结束条件为 `left > right`。
* **左闭右开**：循环结束条件为 `left == right`。


### 两种区间的优缺点 Pros \& Cons


#### 左闭右闭的有点


1. **直观易懂**：包括 `left` 和 `right` 的写法更加接近自然语言的描述，例如 “在 \[left,right] 区间查找目标值”。
2. **处理小区间**：对于某些需要特别处理的小区间问题，左闭右闭可以更容易描述逻辑。


#### 左开右闭的优点


**避免数组越界**：使用左闭右开区间，`right` 永远是无效位置，不会直接访问数组越界的索引。


**逻辑一致性**：左闭右开区间的范围在迭代过程中可以稳定保持逻辑清晰，容易与数学符号对应。


**代码简洁**：由于退出条件是 `left == right`，很多情况下可以直接用 `left` 返回结果，无需做出额外检查。


### 实际应用中的选择 Choosing the Right Interval in Practice


在实际应用中，选择使用左闭右闭还是左闭右开区间，往往取决于具体问题的需求和个人习惯。以下是一些指导原则：


1. **数组索引**：在处理数组索引时，左闭右开区间更加自然，因为数组的索引从 `0` 到 `n-1`，左闭右开可以避免 `n` 的无效访问。
2. **范围划分**：当需要频繁划分范围时，左闭右开区间的逻辑更清晰，减少了混淆和错误。
3. **边界条件**：如果问题中涉及到明确的边界条件，如查找第一个或最后一个满足条件的元素，选择合适的区间类型可以简化逻辑。


### 典型例题分析 Exemplars


#### 1\. 在数组中查找目标值，返回索引


左闭右闭实现：



```
int binarySearch(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}

```

左闭右开实现：



```
int binarySearch(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return -1;
}

```

#### 2\. 在有序数组中找到目标值的插入位置


综上所述，左闭右开更适合这一场景，因为它的区间逻辑更加贴合“边界”问题：



```
int searchInsert(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left; // 返回插入位置
}

```

### 复杂度分析 Complexity Analysis


二分查找的时间复杂度为 O(log2⁡N)，空间复杂度为 O(1)。这种高效性使得二分查找在处理大规模数据时表现出色。然而，二分查找的前提条件是数据必须是有序的，这在某些情况下可能需要额外的排序时间。


### 相关题目 Practice Problems


可以在阅读本文后自己实践一下以下题目：


1. [查找最接近的元素](https://github.com) 在一个升序序列中，查找与给定值最接近的元素。
2. [二分法求函数的零点](https://github.com) 已知函数在某区间内有且只有一个根，使用二分法求出该根。
3. [查找 x](https://github.com) 给定一个升序序列（元素均不重复），在该序列中查找指定的值，若存在则输出对应的下标，否则输出 −1。
4. [二分查找](https://github.com) 在 N 个从小到大排列且不重复的整数中，快速找到指定的数字 t，若找不到则输出 −1。


 \_\_EOF\_\_

   Macw07  - **本文链接：** [https://github.com/Macw07/p/18572027](https://github.com):[slower加速器](https://jisuanqi.org)
 - **关于博主：** 评论和私信会在第一时间回复。或者[直接私信](https://github.com)我。
 - **版权声明：** 本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！
 - **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。
     
