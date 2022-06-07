# 手撕算法常见考点

## 十大排序算法

<img src="https://s1.328888.xyz/2022/05/02/AVhu4.png" alt="复杂度分析" style="zoom: 67%;" />

### **冒泡排序**

- 基本思路：一边比较一边向后两两交换，将最大值 / 最小值冒泡到最后一位，每轮遍历后收缩待排序区间
- 优化：使用一个**变量记录当前轮次的比较是否发生过交换**，如果没有发生交换表示已经有序，不再继续排序

```c++
/*
* 冒泡排序
* 比较交换相邻元素
* 思路简单，仅学习用
*/
class Bubble
{
public:
    void mysort(vector<int> &nums)
    {
        int n = nums.size();
        for (int i = 0; i < n - 1; i++)
        {
            for (int j = 0; j < n - i - 1; j++)
            {
                if (nums[j] > nums[j + 1])
                {
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                }
            }
        }
    }
};

int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Bubble bubble;
    bubble.mysort(nums);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}

```

### **选择排序**

- 基本思路：双重循环遍历数组，每经过一轮比较，找到最小元素的下标，将其交换至**当前待排序区间**的首位。
- 优化：既然每轮遍历时找出了最小值，何不把最大值也顺便找出来呢？这就是二元选择排序的思想。使用二元选择排序，**每轮选择时记录最小值和最大值**，可以把数组需要遍历的范围缩小一倍。

```
/*
* 选择排序
* 思路：找最小元素，放到最终位置
* 特点：时间：O(n²)、非稳定排序
* 适用：数据量少
*/
class Select
{
public:
    void mysort(vector<int> &nums)
    {
        int n = nums.size();
        for (int i = 0; i < n - 1; i++)
        {
            int minPos = i;
            for (int j = i + 1; j < n; j++)
            {
                if (nums[j] < nums[minPos])
                {
                    minPos = j;
                }
            }
            int temp = nums[i];
            nums[i] = nums[minPos];
            nums[minPos] = temp;
        }
    }
};

int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Select select;
    select.mysort(nums);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}
```

**插入排序**

- 基本思想：有新数字加入时，将新数字插入合适的位置
- **交换法：**在新数字插入过程中，不断与前面的数字交换，直到找到自己合适的位置。
- **移动法：**在新数字插入过程中，与前面的数字不断比较，前面的数字不断向后挪出位置，当新数字找到自己的位置后，插入一次即可。

```c++
/*
* 插入排序
* 思路：抓牌一样，插入当前手牌中的适当位置
* 特点：时间：O(n²)
* 适用：基本有序
*/
class Insert
{
public:
    void mysort(vector<int> &nums)
    {
        int n = nums.size();
        for (int i = 1; i < n; i++)
        {
            int pos = i - 1;
            int cur = nums[i];
            while (pos >= 0 && cur < nums[pos])
            {
                nums[pos + 1] = nums[pos];
                pos--;
            }
            nums[pos + 1] = cur;
        }
    }
};
int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Insert insert;
    insert.mysort(nums);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}
```

### **希尔排序**

希尔排序本质上是对插入排序的一种优化，它利用了插入排序的简单，又**克服了插入排序每次只交换相邻两个元素**的缺点。它的基本思想是：

- 将待排序数组按照一定的间隔分为多个子数组，每组分别进行插入排序。这里按照间隔分组指的不是取连续的一段数组，而是**每跳跃一定间隔取一个值组成一组**
- **逐渐缩小间隔**进行下一轮排序
- 最后一轮时，取**间隔为 1，也就相当于直接使用插入排序**。但这时经过前面的「宏观调控」，数组已经基本有序了，所以此时的插入排序只需进行少量交换便可完成

```c++
/*
* 希尔排序
* 思路：间隔分组+自定义排序（这里给出的是冒泡）
* 特点：时间：O(nlogn)、非稳定排序
* 适用：数据量大
*/
class Shell
{
public:
    void mysort(vector<int> &nums)
    {
        int n = nums.size();
        int gap = n / 2;
        while (gap > 0)
        {
            for (int j = gap; j < n; j++)
            {
                int i = j;
                while (i >= gap && nums[i] < nums[i - gap])
                {
                    int temp = nums[i];
                    nums[i] = nums[i - gap];
                    nums[i - gap] = temp;
                    i -= gap;
                }
            }
            gap /= 2;
        }
    }
};
int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Shell shell;
    shell.mysort(nums);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}
```

### **归并排序**

- 基本思想：将两个有序的列表合并成一个有序列表

```c++
/*
* 归并排序
* 思路：递归思想
* 特点：时间：O(nlogn)、空间：O(n)——非原地
* 适用：不受数据影响，所需空间与n成正比
*/
class Merge
{
public:
    void mysort(vector<int> &nums, int left, int right)
    {
        if (left < right)
        {
            int mid = left + (right - left) / 2;
            mysort(nums, left, mid);
            mysort(nums, mid + 1, right);
            merge(nums, left, right);
        }
        return;
    }
    void merge(vector<int> &nums, int left, int right)
    {
        vector<int> temp(nums.size());
        int mid = left + (right - left) / 2;
        int p = left;
        int q = mid + 1;
        int k = left;
        while (p <= mid && q <= right)
        {
            if (nums[p] < nums[q])
                temp[k++] = nums[p++];
            else
                temp[k++] = nums[q++];
        }
        while (p <= mid)
            temp[k++] = nums[p++];
        while (q <= right)
            temp[k++] = nums[q++];
        for (int i = left; i <= right; i++)
            nums[i] = temp[i];
    }
};
int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Merge merge;
    merge.mysort(nums, 0, nums.size() - 1);
    for (int num : nums)
        cout << num << " ";
    return 0;
}
```

### **快速排序**

基本思想：

- 从数组中取出一个数，称之为基数（pivot）
- 遍历数组，将比基数大的数字放到它的右边，比基数小的数字放到它的左边。遍历完成后，数组被分成了左右两个区域
- 将左右两个区域视为两个数组，重复前两个步骤，直到排序完成

```c++
/*
* 快速排序
* 思路：选择中轴元素，比它小的放左，比它大的放右（代码过程很像 小丑在扔三个小球）
* 特点：时间：O(nlogn)、空间：O(logn)、非稳定
* 适用：广泛（最快）
*/
class Quick
{
public:
    void mysort(vector<int> &nums, int left, int right)
    {
        if (start >= end)
            return;

        int mid = partition(nums, left, right);
        mysort(nums, left, mid - 1);
        mysort(nums, mid + 1, end);
    }
    
    int partition(vector<int>& nums, int left, int right) {
        int temp = nums[left];  // 将数组第一个元素作为基数，对数组进行划分
        while(left < right) {
            while(left < right && nums[right] >= temp)
                --right;
            nums[left] = nums[right];
            while(left < right && nums[left] <= temp)
                ++left;
            nums[right] = nums[left];
        }
        nums[left] = temp;
        return left;
    }
    
    // 为避免某些情况（每次选取的pivot都在头部或者尾部），pivot应取随机值
    int ramdomPartition(vector<int>& nums, int left, int right) {
        int mid = rand() % (right - left + 1) + left;
        swap(nums[mid], nums[left]);   //将这个随机值换到左边即可
        return partition(nums, left, right);
    }
};
int main()
{
    srand(time(0));
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Quick quick;
    quick.mysort(nums, 0, nums.size() - 1);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}
```



### 堆排序

基本思想：

- 用数列构建出一个大顶堆，取出堆顶的数字
- 调整剩余的数字，构建出新的大顶堆，再次取出堆顶的数字
- 循环往复，完成整个排序

```c++
/*
* 堆排序
* 思路：升序用大顶堆，每次调整后把最大的移出，再调整...
* 特点：时间：O(nlogn)、非稳定
* 适用：数据结构学习
*/
class Heap
{
public:
    void mysort(vector<int> &nums)
    {
        int n = nums.size();
        // （1）构造初始堆
        // 从第一个非叶子节点（倒数第二行最后一个）开始调整
        // 左右孩子节点中较大的交换到父节点中
        // 注意这里i是自底往上的！
        for (int i = n / 2 - 1; i >= 0; i--)
        {
            headAdjust(nums, n, i);
        }
        // （2）排序
        // 第一步.交换list[0]（最大）和list[i]
        // 第二步.此时list[0]在堆底，固定住，已排好
        // （for循环的i代表len，i--即每次把最后一个排好的忽略掉）
        // 第三步.把无缘无故提上来的幸运儿list[i]再adjust回它应该在的位置
        // （像石头沉入水底）
        // 下一个循环
        for (int i = n - 1; i > 0; i--)
        {
            //交换
            int temp = nums[i];
            nums[i] = nums[0];
            nums[0] = temp;
            //调整
            headAdjust(nums, i, 0);
        }
    }
    // 辅助函数：调整堆
    // 参数说明：nums代表整个二叉树、len是nums的长度、 i代表三个中的根节点
    void headAdjust(vector<int> &nums, int len, int i)
    {
        int index = 2 * i + 1;
        // 这步while的意义在于把较小的沉下去，把较大的提上来
        while (index < len)
        {
            // （1）index指向左右孩子较大的那个
            if (index + 1 < len)
            {
                if (nums[index + 1] > nums[index]) // 说明还有右孩子
                {
                    index = index + 1;
                }
            }
            // （2）比较交换大孩子和根节点
            if (nums[index] > nums[i])
            {
                //交换
                int temp = nums[i];
                nums[i] = nums[index];
                nums[index] = temp;
                //更新
                i = index;
                index = 2 * i + 1;
            }
            else
            {
                break;
            }
        }
    }
};
int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Heap heap;
    heap.mysort(nums);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}

```

### **计数排序**

基本思想：

- 数组中每个元素都是 [1, 9]区间内的整数。那么我们可以构建一个长度为9的数组用于计数，计数数组的下标分别对应区间内的 9个整数。

- 然后遍历待排序的数组，将区间内每个整数出现的次数统计到计数数组中对应下标的位置。最后遍历计数数组，将每个元素输出，输出的次数就是对应位置记录的次数。

```c++
/*
* 计数排序
* 思路：借助足够大的辅助数组，把数字排在一个相对位置不会错的地方，最后并拢
* 特点：时间：O(n+k)、空间：O(n+k)——非原地
* 适用：max和min的差值不大
*/
class Count
{
public:
    void mysort(vector<int> &nums, int min, int max)
    {
        vector<int> temp(max - min + 1);
        for (int num : nums)
        {
            temp[num - min]++;
        }
        int index = 0;
        for (int i = 0; i < temp.size(); i++)
        {
            int cnt = temp[i];
            while (cnt != 0)
            {
                nums[index] = i + min;
                index++;
                cnt--;
            }
        }
    }
};
int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Count count;
    count.mysort(nums, 0, 9);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}
```

### **桶排序**

基本思想：

- 将区间划分为 n 个相同大小的子区间，每个子区间称为一个桶
- 遍历数组，将每个数字装入桶中
- 对每个桶内的数字单独排序，这里需要采用其他排序算法，如插入、归并、快排等
- 最后按照顺序将所有桶内的数字合并起来

```c++
/*
* 桶排序
* 思路：先粗略分类分桶，再各桶排序
* 特点：时间：O(n+k)、空间：O(n+k)——非原地
* 适用：均匀分布的数据
*/
class Bucket
{
public:
    void mysort(vector<int> &nums)
    {
        //（1）初始化桶
        int n = nums.size();
        vector<list<int>> buckets(n);
        //（2）数据放入桶并完成排序
        for (int num : nums)
        {
            int index = getBucketIndex(num);
            insertSort(buckets[index], num);
        }
        //（3）从桶取数据，放入nums
        int index = 0;
        for (list<int> bucket : buckets)
        {
            for (int num : bucket)
            {
                nums[index] = num;
                index++;
            }
        }
    }
    //辅助函数一：获得桶的序号
    int getBucketIndex(int num)
    {
        return num / 3;
    }
    //辅助函数二：把数据插入对应桶(这里用的插入排序)
    void insertSort(list<int> &bucket, int num)
    {
        int n = bucket.size();
        bool flag = true;
        for (auto it = bucket.begin(); it != bucket.end(); it++)
        {
            if (num <= *it)
            {
                bucket.insert(it, num);
                flag = false;
                break;
            }
        }
        if (flag)
            bucket.push_back(num);
    }
};
int main()
{
    vector<int> nums = {9, 7, 5, 3, 1, 0, 8, 4, 6, 2};
    Bucket b;
    b.mysort(nums);
    for (int num : nums)
    {
        cout << num << " ";
    }
    return 0;
}

```



### **基数排序**

基本思想：我们大脑中对日期排序的思维过程是：先看年份，再比较一下月份，最后比较具体几号。**利用多关键字进行排序**的思想就是基数排序。

- 找出数组中最大的数字的位数 maxDigitLength
- 获取数组中每个数字的基数
- 遍历 maxDigitLength 轮数组，每轮按照基数对其进行排序（每轮对基数进行排序非常适合使用我们在上面学习的**计数排序**算法）

```c++
/*
* 基数排序
* 思路：桶排序的一种。 按数位分桶：从低位开始 -> 分桶、收集 -> 下一位...
* 特点：时间：O(kn)、空间：O(n+k)——非原地
* 适用：max和min的差值不大
*/
class Radix
{
public:
    void mysort(vector<int> &nums, int d)
    {
        int p = 1;
        int n = nums.size();
        vector<vector<int>> buckets(10, vector<int>(n));
        vector<int> order(10);
        while (p < d)
        {
            //（1）进行一轮分桶
            for (int num : nums)
            {
                int index = num / p % 10;           //获取桶号
                buckets[index][order[index]] = num; // num放入index号桶的第
                order[index] 位置
                    order[index]++; //位置++
            }
            //（2）进行一轮排序
            int k = 0;
            for (int i = 0; i < 10; i++)
            {
                if (order[i] == 0)
                    continue;
                for (int j = 0; j < order[i]; j++)
                {
                    nums[k] = buckets[i][j];
                    k++;
                }
                order[i] = 0; //各桶计数器清零
            }
            p *= 10;
        }
    }
};
int main()
{
    vector<int> nums = {999, 765, 780, 215, 13, 66, 230, 450, 699, 21};
    Radix radix;
    radix.mysort(nums, 1000);
    for (int num : nums)
        cout << num << " ";
    return 0;
}
```



## 数组相关

### 原地哈希

如果数组里面各个元素的大小都是可表达的（比如小于数组长度），那么就可以对数组的各个元素在元素组中进行映射（简单来说就是一个萝卜一个坑），从而在不浪费额外空间下，迅速找到重复元素。

通过不断交换，让元素放在正确的位置

**例题：**

1. [442. 数组中重复的数据](https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/)（中等）
2. [41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)（困难）
3. [448. 找到所有数组中消失的数字](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/)（简单）

```c++
// 41. 缺失的第一个正数的题解
int firstMissingPositive(vector<int>& nums) {
    for(int i = 0; i < nums.size(); ++i) {
        // nums[i]与它应该在的位置的元素比较
        while(nums[i] > 0 && nums[i] <= nums.size() && nums[i] != nums[nums[i] - 1]) {
            swap(nums[i], nums[nums[i] - 1]);
        }
    }

    for(int i = 0; i < nums.size(); ++i) {
        if(nums[i] != i + 1)
            return i + 1;
    }

    return nums.size() + 1;
}
```



### 第 k 大/小的数

**这种题型两种思路：**

- 如果数是一个一个输入进去的，可以考虑 `priority_queue` （大顶堆/小顶堆）
- 如果是给定数组的话，更应该考虑快速排序 + 二分查找

**例题：**

1. ##### [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int left = 0, right = nums.size() - 1;
        while(left <= right) {
            int mid = randomPartition(nums, left, right);
            if(mid < nums.size() - k) {
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }

        return nums[left];
    }

private:
    int partition(vector<int>& nums, int left, int right) {
        int pivot = nums[left];
        while(left < right) {
            while(left < right && nums[right] >= pivot)
                --right;
            nums[left] = nums[right];
            while(left < right && nums[left] <= pivot)
                ++left;
            nums[right] = nums[left];
        }
        nums[left] = pivot;
        return left;
    }

    int randomPartition(vector<int>& nums, int left, int right) {
        int mid = rand() % (right - left + 1) + left;
        swap(nums[left], nums[mid]);
        return partition(nums, left, right);
    }
};
```





### 滑动窗口

1. 定义：
滑动窗口，就是有一个大小可变的窗口，左右两端方向一致的向前滑动（右端固定，左端滑动；左端固定，右端滑动）。
2. 适用场景：
* 一般是字符串或者列表
* 一般是要求最值（最大长度，最短长度等等）或者子序列
3. 例题：
* [晚上总结](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/solution/yi-ge-mo-ban-miao-sha-10dao-zhong-deng-n-sb0x/)
* [643. 子数组最大平均数 I](https://leetcode-cn.com/problems/maximum-average-subarray-i/)
* [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)
* [209. 长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)
* [1695. 删除子数组的最大得分](https://leetcode-cn.com/problems/maximum-erasure-value/)
* [438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/ )和[567. 字符串的排列](https://leetcode-cn.com/problems/permutation-in-string/)（两个基本是同一道题）
* [1004. 最大连续1的个数 III](https://leetcode-cn.com/problems/max-consecutive-ones-iii/)和[1493. 删掉一个元素以后全为 1 的最长子数组](https://leetcode-cn.com/problems/longest-subarray-of-1s-after-deleting-one-element/)
* [424. 替换后的最长重复字符](https://leetcode-cn.com/problems/longest-repeating-character-replacement/)和[1208. 尽可能使字符串相等](https://leetcode-cn.com/problems/get-equal-substrings-within-budget/)（前缀和的思想）
* [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)
4. 模板（伪代码）：
```c++
int templateofWindow(vector<int>& nums) {
    int start = 0, end = 0;
    int result = 0;

    // 遍历整个数组
    while(end < nums.size()) {
        
        // 处理当前右指针位置的数据
        if(/*当前位置的数据满足要求*/) {
            ++cntZero;
        }

        // 当前窗口不满足条件
        while(/*当前窗口不满足条件*/) {
            if(nums[start] == 0)
                --cntZero;
            // 右移左指针
            ++start;
        }

        // 计算当前窗口的大小
        result = max(result, end - start + 1);
        // 右移右指针
        ++end;
    }

    return result;
}
```

### 约瑟夫环问题

约瑟夫问题是个著名的问题：N个人围成一圈，第一个人从1开始报数，报M的将被杀掉，下一个人接着从1开始报。如此反复，最后剩下一个，求最后的胜利者。
例如只有三个人，把他们叫做A、B、C，他们围成一圈，从A开始报数，假设报2的人被杀掉。

**例题：**

1. [1823. 找出游戏的获胜者](https://leetcode-cn.com/problems/find-the-winner-of-the-circular-game/)
2. [剑指 Offer 62. 圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)
3. 

<img src="https://assets.leetcode.com/uploads/2021/03/25/ic234-q2-ex11.png" alt="约瑟夫环示意图" style="zoom:67%;" />

- **队列模拟**

被淘汰的pop出队列，否则重新入队

```c++
int lastRemaining(int n, int m)
{
    queue<int> que;
    for (int i = 0; i < n; ++i)
    {
        que.push(i);
    }

    for (int i = 0; i < n - 1; ++i)
    {
        for (int j = 1; j < m; ++j)
        {
            int cur = que.front();
            que.pop();
            que.emplace(cur);
        }
        que.pop();
    }

    return que.front();
}
```

- **逆向思维**

约瑟夫环的递推公式
$$
f(N,M)=(f(N−1,M)+M) \% N
$$
f ( N , M )表示，N个人报数，每报到M时杀掉那个人，最终胜利者的编号
f ( N − 1 , M )表示，N-1个人报数，每报到M时杀掉那个人，最终胜利者的编号

简单来说就是：

- 只有一人报数时，最后胜利者的编号一定是0，即f ( 1 , M )=0
- 而有两个人报数时，淘汰一个人后，相当于把胜利者的编号前移M位**（每杀掉一个人，下一个人成为头，相当于把数组向前移动M位）**
- 所以从后往前推就是将胜利者的编号后移M位

```c++
/*递归版*/
int lastRemaining(int n, int m)
{
    if (n == 0)
        return 0;
    return (lastRemaining(n - 1, m) + m) % n;
}

/*迭代版*/
int lastRemaining(int n, int m)
{
    int result = 0;
    // 从剩两个人开始倒推
    for (int i = 2; i <= n; ++i)
    {
        result = (result + m) % i;
    }

    return result;
}
```

### 二分查找（binary search）

主要题目提到有序，大概率是二分

或者是第 k 大/第 k 小，大概率是二分或者 minHeap/maxHeap

**主要搞清楚以下几个点：**

1. `while(left <= right)` 还是 `while(left < right)`

   有等号代表`[left, right]`，每次遍历需要改变左右指针 `left = mid + 1` 或 `right = mid - 1`

   没等号代表`[left, right)`，每次遍历仅需要改变左指针 `left = mid + 1`

2. 是只找一个还是找左右边界

   - 找一个，当 mid 满足条件就可以返回了
   - 找左右边界在 `mid == target` 时，仍需移动左右指针
   
3. 是以值域二分还是以下标二分

#### 整体有序的二分查找

**例题（一维数组的二分查找）：**

1. [704.二分查找](https://leetcode-cn.com/link/?target=https%3A%2F%2Fprogrammercarl.com%2F0704.二分查找.html)
2. [35.搜索插入位置](https://leetcode-cn.com/link/?target=https%3A%2F%2Fprogrammercarl.com%2F0035.搜索插入位置.html)
3. [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```c++
// 找一个
int getIndex(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] == target) {
            return mid;
        }
        else if(nums[mid] < target) {
            ++left;
        }
        else {
            --right;
        }
    }
    return -1;
}

// 找右边界
// 二分查找，寻找target的右边界（不包括target）
// 如果rightBorder为没有被赋值（即target在数组范围的左边，例如数组[3,3]，target为2），为了处理情况一
int getRightBorder(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
    int rightBorder = -2; // 记录一下rightBorder没有被赋值的情况
    while (left <= right) { // 当left==right，区间[left, right]依然有效
        int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
        if (nums[middle] > target) {
            right = middle - 1; // target 在左区间，所以[left, middle - 1]
        } else { // 当nums[middle] == target的时候，更新left，这样才能得到target的右边界
            left = middle + 1;
            rightBorder = left; // 边界包括target这句放前面就行
        }
    }
    return rightBorder;
}

// 找左边界
// 二分查找，寻找target的左边界leftBorder（不包括target）
// 如果leftBorder没有被赋值（即target在数组范围的右边，例如数组[3,3],target为4），为了处理情况一
int getLeftBorder(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
    int leftBorder = -2; // 记录一下leftBorder没有被赋值的情况
    while (left <= right) {
        int middle = left + ((right - left) / 2);
        if (nums[middle] >= target) { // 寻找左边界，就要在nums[middle] == target的时候更新right
            right = middle - 1;
            leftBorder = right; // 边界包括target这句放前面就行
        } else {
            left = middle + 1;
        }
    }
    return leftBorder;
}
```



#### 部分有序的二分查找

**例题（二维数组的二分查找）：**

1. [668. 乘法表中第k小的数](https://leetcode.cn/problems/kth-smallest-number-in-multiplication-table/)（值域二分）
2. [378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/)（值域二分）
2. [剑指 Offer 11. 旋转数组的最小数字](https://leetcode.cn/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)（部分有序一维数组的二分查找）

```c++
// 378. 有序矩阵中第 K 小的元素
class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        int left = 1, right = m * n;
        while(left <= right) {
            int mid = right - (right - left) / 2;
            // int mid = (right + left) / 2;
            if(numsBelowX(m, n, mid) < k) {
                left = mid + 1;
            }
            else {
                right = mid - 1;
            }
        }
        return left;
    }

private:
    int numsBelowX(int m, int n, int x) {
        int result = 0;
        for(int i = 1; i <= m ; ++i) {
            result += min(x / i, n);
        }

        return result;
    }
};

// 剑指 Offer 11. 旋转数组的最小数字
int minArray(vector<int>& numbers) {
    // 数组部分有序，考虑二分查找
    int left = 0, right = numbers.size() - 1;
    while(left <= right) {
        int mid = (left + right) / 2;
        if(numbers[mid] < numbers[right]) {  // 这里不能写right=mid-1，因为可能会跳出右区间的最小值
            right = mid;
        }
        else if(numbers[mid] > numbers[right]) { //此时最小值一定在左边区间
            left = mid + 1;
        }
        else { // 重复的话只能由从右边界开始试了
            --right;
        }
    }
    return numbers[left];
}
```





### 子数组数量问题

**给定一个数组，求满足【条件】的子数组数量。**

注意点：

- `curLen` 表示：以 `nums[i]` 结尾的子数组长度，也表示以 `nums[i]` 结尾的子数组个数（如 `[1, 2, 3]` 中的 `curLen = 3` 表示了 `[3]、[2, 3]、[1, 2, 3]` 正好三个子数组）
- 满足条件时，增加长度 `curLen` ，否则重置长度 `curLen` 为 0 ；
- 条件为区间 [L,  R] ，最大元素满足大于等于L小于等于R的子数组个数 = 最大元素**小于等于R**的子数组个数 - 最大元素**小于L**的子数组个数



**例题：**

1. [795. 区间子数组个数](https://leetcode.cn/problems/number-of-subarrays-with-bounded-maximum/)
2. 

```c++
// 统计 <= k 的子数组数目
int countSubArray (vector<int>& nums, int k) {
    int res = 0, curLen = 0;
    for(int i = 0; i < nums.size(); ++i) {
        if(nums[i] <= k) { //满足条件才增加长度
            ++curLen;
            res += curLen;
        }
        else {
            curLen = 0;
        }
    }

    return res;
}
```







### 接雨水

**例题：**

[42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

主要有三种方法：双指针、动态规划、单调栈

- 双指针法

![](https://img-blog.csdnimg.cn/20210402091208445.png)

简单来说，一个根柱子能接雨水的前提是，左右两侧存在比当前柱子高的柱子，所以双指针就是向两边寻找左右两侧最高的柱子。

```c++
// 时间复杂度O(n²)
int trap(vector<int>& height) {
    int area = 0;
    for(int i = 0; i < height.size(); ++i) {
        if(i == 0 || i == height.size() - 1)    continue;

        int lheight = height[i], rheight = height[i];
        for(int l = i - 1; l >= 0; --l) {
            if(lheight < height[l])
                lheight = height[l];
        }
        for(int r = i + 1; r < height.size(); ++r) {
            if(rheight < height[r])
                rheight = height[r];
        }

        // 左右两侧存在比当前柱子高的，才能接到雨水
        int h = min(lheight, rheight) - height[i];
        if(h > 0)   area += h;
    }

    return area;
}
```

明显做了很多重复计算

- 动态规划

为了得到两边的最高高度，使用了双指针来遍历，每到一个柱子都向两边遍历一遍，这其实是有重复计算的。把每一个位置的左边最高高度记录在一个数组上（maxLeft），右边最高高度记录在一个数组上（maxRight）。这样就避免了重复计算，这就用到了动态规划。

（1）即从左向右遍历：maxLeft[i] = max(height[i], maxLeft[i - 1]);

（2）从右向左遍历：maxRight[i] = max(height[i], maxRight[i + 1]);

```c++
// 时间复杂度O(n)
// 空间复杂度O(n)
int trap(vector<int>& height) {
    if(height.size() <= 2)  return 0;
    vector<int> maxLeft(height.size(), 0);
    vector<int> maxRight(height.size(), 0);

    int size = height.size();

    // 记录每个柱子左边柱子的最大高度
    maxLeft[0] = height[0];
    for(int i = 1; i < size; ++i) {
        maxLeft[i] = max(maxLeft[i - 1], height[i]);
    }
    // 记录每个柱子右边柱子的最大高度
    maxRight[size - 1] = height[size - 1];
    for(int i = size - 2; i >= 0; --i) {
        maxRight[i] = max(maxRight[i + 1], height[i]);
    }

    int area = 0;
    for(int i = 0; i < size; ++i) {
        int h = min(maxLeft[i], maxRight[i]) - height[i];
        if(h > 0)   area += h;
    }

    return area;
}
```



- 单调栈

解法参照：[代码随想录 (programmercarl.com)](https://programmercarl.com/0042.接雨水.html#单调栈解法)

```c++
int trap(vector<int>& height) {
    stack<int> stk;  // 存放下标
    stk.emplace(0);
    int area = 0;
    for(int i = 1; i < height.size(); ++i) {
        if(height[i] < height[stk.top()]) {
            stk.emplace(i);
        }
        else if(height[i] == height[stk.top()]) {
            stk.pop();
            stk.emplace(i);
        }
        else {
            while(!stk.empty() && height[i] > height[stk.top()]) {
                int mid = stk.top();
                stk.pop();
                if(!stk.empty()) {
                    int h = min(height[stk.top()], height[i]) - height[mid];
                    int w = i - stk.top() - 1;
                    area += w * h;
                }
            }
            stk.emplace(i);
        }
    }

    return area;
}
```





### 区间重叠问题

这种就两个思路：区间排序（截断数组）和线段树

**例题：**

1. [732. 我的日程安排表 III](https://leetcode.cn/problems/my-calendar-iii/)
2. [剑指 Offer II 074. 合并区间](https://leetcode.cn/problems/SsGoHC/)
3. [452. 用最少数量的箭引爆气球](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/)

```c++
// 732. 我的日程安排表 III
// [start, end)，左闭右开，左端加1，右端截断（减一）
class MyCalendarThree {
public:
    MyCalendarThree() {

    }
    
    int book(int start, int end) {
        int res = 0;
        int maxBook = 0;
        ++cnt[start];
        --cnt[end];
        for(auto& [_, freq] : cnt) {
            maxBook += freq;
            res = max(maxBook, res);
        }

        return res;
    }

private:
    map<int, int> cnt;
};
```



## 二叉树相关

### 二叉树的定义（手撕时需要自己写）

```c++
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
```

### 二叉树的输入（ACM模式）



### 二叉树的遍历

**前序遍历**

- ##### 迭代版

```c++
// 1、先访问到树的最底层，将沿路的节点压入栈并处理节点（一路向左）
// 2、处理最后一个左节点后，将右节点放入栈中
void preorderTraversal(TreeNode* root) {
	stack<TreeNode*> stk;
    TreeNode* cur = root;
    while(cur != nullptr || !stk.empty()) {
        // 一路向左
		while(cur != nullptr) {
            doSomething(cur->val); // 处理当前节点
            stk.push(cur);
            cur = cur->left;
        }
        
        cur = stk.top();
        stk.pop();
        cur = cur->right;
    }
}
```

- ##### 递归版

```c++
void preorderTraversal(TreeNode* root) {
    if(root == nullptr)	return;
    // 中（处理当前节点）
    doSomething(root->val);
    // 左
    preorderTraversal(root->left);
    // 右
    preorderTraversal(root->right);
}
```

**中序遍历**

- ##### 迭代版

```c++
// 1、先访问到树的最底层，将沿路的节点压入栈（一路向左）
// 2、将栈中的中节点取出来处理
// 3、将右节点放入栈中
void inorderTraversal(TreeNode* root) {
    stack<TreeNode*> stk;
    TreeNode* cur = root;
    
    while(cur != nullptr || !stk.empty()) {
        // 一路向左
        while(cur != nullptr) {
            stk.push(cur);
            cur = cur->left;
        }
        
        cur = stk.top();
        stk.pop();
        doSomething(cur->val);	// 中
        // 右
        cur = cur->right;
    }
}
```

- ##### 递归版

```c++
void inorderTraversal(TreeNode* root) {
    if(root == nullptr)	return;
    
    inorderTraversal(root->left);	// 左
    doSomething(root->val);	// 中
    inorderTraversal(root->right); // 右
}
```

**后序遍历**

- ##### 迭代版

```c++
// 1、将左节点全部放入栈中
void postorderTraversal(TreeNode* root) {
	stack<TreeNode*> stk;
    TreeNode *pre = nullptr, *cur = root;
    
    while(cur != nullptr || !stk.empty()) {
        // 一路向左
        while(cur != nullptr) {
            stk.push(cur);
            cur = cur->left;
        }
        
        cur = stk.top(); // 此处不出栈，遍历完右子树再出栈
        // 右子树不为空 且 没有遍历过
        if(cur->right != nullptr && cur->right != pre) {
            cur = cur->right;
        }
        else {
            stk.pop();
            doSomething(cur->val);	// 处理节点
            pre = cur;
            cur = nullptr;
        }
    }
}
```

- ##### 递归版

```c++
void postorderTraversal(TreeNode* root) {
    if(root == nullptr)	return;
    
    inorderTraversal(root->left);	// 左
    inorderTraversal(root->right);  // 右
    doSomething(root->val);	        // 中
}
```

**层序遍历**

```c++
// 1、记录队列的length
// 2、遍历队列length个节点并进行处理
// 3、将处理节点的左右节点放入队列中
void bfs(TreeNode* root) {
    queue<TreeNode*> que;
    if(root != nullptr)
        que.push(root);
    
    while(!que.empty()) {
        int size = que.size();
        // 遍历该层的所有节点
        while(size > 0) {
            TreeNode* cur = que.front();
            que.pop();
            
            doSomething(cur->val);	// 处理节点
            if(cur->left != nullptr)
                que.push(cur->left);
            if(cur->right != nullptr)
                que.push(cur->right);
            --size;
        }
    }
}
```



### BST的二分查找

**例题：**

1. [面试题 04.06. 后继者](https://leetcode.cn/problems/successor-lcci/)

**基本思想：**

```
输入: root = [5,3,6,2,4,null,null,1], p = 6

      5
     / \
    3   6
   / \
  2   4
 /   
1

输出: null
```

BST的核心就是左小右大

要从BST里找一个数，就是大于target，就向左走（当然，**如果找大于target的第一个数还要一直往左走**，并且每次都记录一下节点），小于target，就向右走。

```c++
TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
    int target = p->val;
    TreeNode *cur = root, *result = nullptr;
    while(cur != nullptr) {
        if(cur->val > target) {
            result = cur;
            cur = cur->left;
        }
        else {
            cur = cur->right;
        }
    }

    return result;
}
```





### 二叉树的编码

**例题：**

1. [297. 二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)

**流式思想**

考虑前序遍历，每个节点的值用`" "`隔开，空指针用`"#"`表示，然后使用`istringstream`每次读入一个节点。

```c++
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if(root == nullptr) return "#";

        return to_string(root->val) + " " + serialize(root->left) + " " + serialize(root->right);
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        istringstream ss(data);
        return mydeserialize(ss);
    }

private:
    TreeNode* mydeserialize(istringstream& ss) {
        string temp;
        ss >> temp;
        if(temp == "#") return nullptr;

        TreeNode* root = new TreeNode(stoi(temp));
        root->left = mydeserialize(ss);
        root->right = mydeserialize(ss);
        return root;
    }
};
```





## 图相关

### 建图模板
如果可以确定图中每个节点的范围，用邻接矩阵`vector<int,unordered_set>`或`vector<vector<int>>`，否则用邻接表`unordered_map<int,unordered_set>>`

### BFS和DFS的模板
DFS:需要记录路径信息（节点值、路径长度等）或只要找一条符合要求的路径
BFS:找最短路径

以下模板均以：[剑指 Offer II 105. 岛屿的最大面积](https://leetcode.cn/problems/ZL6zAn/)为例子



#### DFS模板

- 迭代形式（用栈实现）

```c++
int getArea(const vector<vector<int>>& grid, vector<vector<bool>>& visted, int i, int j) {
    // 队列存放点的坐标(i,j)
    stack<vector<int>> stk;
    int area = 0;
    stk.push({i, j});
    visted[i][j] = true;

    vector<vector<int>> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    while(!stk.empty()) {
        vector<int> pos = que.front();
        stk.pop();
        ++area;

        for(vector<int>& dir : dirs) {
            int r = pos[0] + dir[0], c = pos[1] + dir[1];
            if(r >= 0 && r < grid.size()
               && c >= 0 && c < grid[0].size()
               && grid[r][c] == 1 && !visted[r][c]) {
                stk.push({r, c});
                visted[r][c] = true;
            }
        }
    }

    return area;
}
```



- 递归形式

```c++
int getArea(const vector<vector<int>>& grid, vector<vector<bool>>& visted, int i, int j) {
    int area = 1;
    visted[i][j] = true;
    vector<vector<int>> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    
    for(vector<int>& dir : dirs) {
        int r = pos[0] + dir[0], c = pos[1] + dir[1];
        if(r >= 0 && r < grid.size()
           && c >= 0 && c < grid[0].size()
           && grid[r][c] == 1 && !visted[r][c]) {
			area += getArea(gird, visited, r, c);
        }
    }
}
```



#### BFS模板

单纯的遍历：不需要记录队列的长度

```c++
int getArea(const vector<vector<int>>& grid, vector<vector<bool>>& visted, int i, int j) {
    // 队列存放点的坐标(i,j)
    queue<vector<int>> que;
    int area = 0;
    que.push({i, j});
    visted[i][j] = true;

    vector<vector<int>> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    while(!que.empty()) {
        vector<int> pos = que.front();
        que.pop();
        ++area;

        for(vector<int>& dir : dirs) {
            int r = pos[0] + dir[0], c = pos[1] + dir[1];
            if(r >= 0 && r < grid.size()
               && c >= 0 && c < grid[0].size()
               && grid[r][c] == 1 && !visted[r][c]) {
                que.push({r, c});
                visted[r][c] = true;
            }
        }
    }

    return area;
}
```



统计层数：需要记录队列的长度

```c++
vector<pair<int, int>> dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
// 从 (srcX, srcY) 到 (dstX, dstY) 需要走的最小步数（也就是层数） 
int dfs(vector<vector<int>>& forest, int srcX, int srcY, int dstX, int dstY) {
    if(srcX == dstX && srcY == dstY)
        return 0;

    queue<pair<int, int>> que;
    int M = forest.size(), N = forest[0].size();
    int steps = 0;
    vector<vector<bool>> visited(M, vector<bool>(N, false));
    que.emplace(srcX, srcY);
    visited[srcX][srcY] = true;

    while(!que.empty()) {
        //每次到下一层，先把层数 +1 
        ++steps;
        int size = que.size();
        for(int i = 0; i < size; ++i) {
            pair<int, int> p = que.front();
            que.pop();
            int sx = p.first, sy = p.second;

            for(const pair<int, int>& dir : dirs) {
                int x = sx + dir.first;
                int y = sy + dir.second;

                if(x >= 0 && x < M && y >= 0 && y < N && 
                    !visited[x][y] && forest[x][y] != 0) {
                    if(x == dstX && y == dstY) //在下一层到达终点，直接返回steps
                        return steps;
                    visited[x][y] = true;
                    que.push({x, y});
                }
            }
        }
    }

    return -1;
}
```



### 存在状态变化方格的图搜索

这种问题需要多维护一个数组来记录代价。

例题：

1. [1368. 使网格图至少有一条有效路径的最小代价](https://leetcode.cn/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/)

```c++
// Dijkstra 算法
using PII = pair<int, int>;

class Solution {
private:
    static constexpr int dirs[4][2] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

public:
    int minCost(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<int> dist(m * n, INT_MAX);   // 当前点到起点的代价
        vector<int> seen(m * n, 0);         // 记录当前点是否遍历过，避免重复遍历
        dist[0] = 0;
        priority_queue<PII, vector<PII>, greater<PII>> q;
        q.emplace(0, 0);
        
        while (!q.empty()) {
            auto [cur_dis, cur_pos] = q.top();
            q.pop();
            if (seen[cur_pos]) {
                continue;
            }
            seen[cur_pos] = 1;
            int x = cur_pos / n;
            int y = cur_pos % n;
            for (int i = 0; i < 4; ++i) {
                int nx = x + dirs[i][0];
                int ny = y + dirs[i][1];
                int new_pos = nx * n + ny;
                // 当前的代价 + 下一步的代价
                int new_dis = cur_dis + (grid[x][y] != i + 1);
                
                // 更新代价
                if (nx >= 0 && nx < m && ny >= 0 && ny < n && new_dis < dist[new_pos]) {
                    dist[new_pos] = new_dis;
                    q.emplace(new_dis, new_pos);
                }
            }
        }

        return dist[m * n - 1];
    }
};

// 0-1 BFS
using PII = pair<int, int>;

class Solution {
private:
    static constexpr int dirs[4][2] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

public:
    int minCost(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<int> dist(m * n, INT_MAX);
        vector<int> seen(m * n, 0);
        dist[0] = 0;
        deque<int> q;
        q.push_back(0);
        
        while (!q.empty()) {
            auto cur_pos = q.front();
            q.pop_front();
            if (seen[cur_pos]) {
                continue;
            }
            seen[cur_pos] = 1;
            int x = cur_pos / n;
            int y = cur_pos % n;
            for (int i = 0; i < 4; ++i) {
                int nx = x + dirs[i][0];
                int ny = y + dirs[i][1];
                int new_pos = nx * n + ny;
                int new_dis = dist[cur_pos] + (grid[x][y] != i + 1);
                
                if (nx >= 0 && nx < m && ny >= 0 && ny < n && new_dis < dist[new_pos]) {
                    dist[new_pos] = new_dis;
                    if (grid[x][y] == i + 1) {
                        q.push_front(new_pos);
                    }
                    else {
                        q.push_back(new_pos);
                    }
                }
            }
        }

        return dist[m * n - 1];
    }
};

```



### 求深度

### 求拓扑排序

每次从有向无环图 `graph` 中取出一个入度为0的节点添加到拓扑排序序列 `order` 中，然后删除该节点及所有以它为起点的边。重复这个步骤，直到图为空或图中不存在入度为0的节点：
* 图为空：图是有向无环图，此时`order`为该图的一个拓扑排序序列
* 图不为空且不存在入度为0的节点：图中一定有环

例题：[剑指 Offer II 113. 课程顺序](https://leetcode-cn.com/problems/QA2IGt/)->BFS
```cpp
// n:节点数目 prerequisites：矩阵形式的邻接表
vector<int> findOrder(int n, vector<vector<int>>& prerequisites) {
    // 求有向图的拓扑排序

    unordered_map<int, vector<int>> graph;    // 用邻接表表示的图
    vector<int> inDegrees(n, 0);              // 记录入度的数组
    queue<int> que;                           // 存放入度为0的节点，用于BFS
    vector<int> order;                        // 存放结果

    // 建图
    for(const vector<int>& prerequisite : prerequisites) {
        graph[prerequisite[1]].push_back(prerequisite[0]);
        ++inDegrees[prerequisite[0]];
    }

    // 将入度为0的节点放入队列
    for(int i = 0; i < n; ++i) {
        if(inDegrees[i] == 0)   que.push(i);
    }

    // BFS
    while(!que.empty()) {
        int cur = que.front();
        que.pop();
        order.push_back(cur);

        for(const int& neighbor : graph[cur]) {
            --inDegrees[neighbor];
            if(inDegrees[neighbor] == 0)    que.push(neighbor);
        }
    }

    if(order.size() == n)  return order;
    return {};
}
```

### 并查集
并查集主要用于解决以下问题：
  1. 图中的子图数目是多少
  2. 最大的子图中有多少个节点

例题：

1. [剑指 Offer II 116. 省份数量](https://leetcode-cn.com/problems/bLyHh0/)->并查集
2. [200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/)



**初始化:**<br>
![初始化](https://pic3.zhimg.com/80/v2-3153d1689405b4122b05d966b75b4e6a_720w.jpg)

**合并：**<br>
![合并](https://pic4.zhimg.com/80/v2-f3c350c735a884d1b264c5e91c9edfdb_720w.jpg)

```cpp
// 统计子图数目
int findCircleNum(vector<vector<int>>& isConnected) {
    vector<int> fathers(isConnected.size());
    for(int i = 0; i < fathers.size(); ++i) {
        // 初始化：每个子图都与自己连通
        fathers[i] = i;
    }

    // 初始有count个子图，然后开始合并子图
    int count = isConnected.size();
    for(int i = 0; i < isConnected.size(); ++i) {
        for(int j = i + 1; j < isConnected[i].size(); ++j) {
            // 如果两个子图连通，并且可以合并（两个子图可能已经连通）
            if(isConnected[i][j] == 1 && union(fathers, i, j)) {
                --count;
            }
        }
    }

    return count;
}

int findFather(vector<int>& fathers, int i) {
    if(fathers[i] != i) {
        // 递归找根节点
        fathers[i] = findFather(fathers, fathers[i]);
    }

    return fathers[i];
}

bool union(vector<int>& fathers, int i, int j) {
    int fatherI = findFather(fathers, i);
    int fatherJ = findFather(fathers, j);

    // 合并两个子图
    if(fatherI != fatherJ) {
        fathers[fatherI] = fatherJ;
        return true;
    }

    // 两个子图本来就连通
    return false;
}
```



#### 并查集类的模板（必背）

```c++
class UnionFind {
public:
    UnionFind(vector<vector<char>>& grid) {  //二维数组需要将坐标转换为一维坐标
        count = 0;
        int m = grid.size();
        int n = grid[0].size();
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (grid[i][j] == '1') {
                    parent.push_back(i * n + j);
                    ++count;
                }
                else {
                    parent.push_back(-1);
                }
                rank.push_back(0);
            }
        }
    }

    int find(int i) {
        if (parent[i] != i) {
            parent[i] = find(parent[i]);
        }
        return parent[i];
    }

    void Union(int x, int y) {
        int rootx = find(x);
        int rooty = find(y);
        if (rootx != rooty) {
            if (rank[rootx] < rank[rooty]) { // 层数小的接到层数大的节点（减小整体层数）
                parent[rootx] = rooty;
            }
            else if(rank[rooty] < rank[rootx]) {
                parent[rooty] = rootx;
            }
            else {
                parent[rootx] = rooty;
                ++rank[rooty];
            }
            --count;
        }
    }

    int getCount() const {
        return count;
    }

private:
    vector<int> parent; // parent[i]表示第i个元素所指向的父节点
    vector<int> rank;	// rank[i]表示以i为根的集合所表示的树的层数
    int count;          // 连通的子图数
};

// 200. 岛屿数量
// 使用起来也比较简单
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int nr = grid.size();
        if (!nr) return 0;
        int nc = grid[0].size();

        UnionFind uf(grid);  //建立并查集
        int num_islands = 0;
        for (int r = 0; r < nr; ++r) {
            for (int c = 0; c < nc; ++c) {
                if (grid[r][c] == '1') {  //不断连通子图
                    grid[r][c] = '0';
                    if (r - 1 >= 0 && grid[r-1][c] == '1') uf.Union(r * nc + c, (r-1) * nc + c);
                    if (r + 1 < nr && grid[r+1][c] == '1') uf.Union(r * nc + c, (r+1) * nc + c);
                    if (c - 1 >= 0 && grid[r][c-1] == '1') uf.Union(r * nc + c, r * nc + c - 1);
                    if (c + 1 < nc && grid[r][c+1] == '1') uf.Union(r * nc + c, r * nc + c + 1);
                }
            }
        }

        return uf.getCount();  //获取连通子图个数
    }
};

```



## 字符串相关

### 实现strcpy()

把从strsrc地址开始且含有'\0'结束符的字符串复制到以strdest开始的地址空间，返回值的类型为char*

**注意的点：**

1. 源字符串使用const，避免被修改
2. 函数入口加入空指针判断
3. 返回值为`char*`，使函数支持链式表达式

**缺陷：**

1. 当src的长度超过dest的长度，导致数据写入未知内存->最好使用`strncpy()`（用户自己声明拷贝多少个字符）

```c++
char* strcpy(char* dest, const char* src) {
    assert(dest != nullptr && src != nullptr);
    char* address = dest;
    while((*dest++ = *src++) != '\0');
    return address;
}

char* strncpy(char* dest, const char* src, size_t n) {
    assert(dest != nullptr && src != nullptr);
    char* ret = dest;
    while(n-- && (*dest++ = *src++) != '\0');
    return ret;
}
```



### 实现strStr()

### 实现strlen()

计算给定字符串的长度。

```c++
int strlen(const char* str) {
    assert(str != nullptr);
    int len;
    while(*str++ != '\0') {
        ++len;
    }
    return len;
}
```



### 实现strcat()

把src所指字符串添加到dest结尾处。

```c++
char* strcat(char* dest, const char* src) {
    assert(dest != nullptr && src != nullptr);
    char* ret = dest;
    // 找到dest的结尾
    while(*dest != '\o') {
        dest++;
    }
    while((*dest++ = *src++) != '\0') {}
    return ret;
}
```



### 实现strcmp()

比较两个字符串设这两个字符串为str1，str2

若`str1 == str2`，则返回零

若`str1 < str2`，则返回负数

若`str1 > str2`，则返回正数

```c++
int strcmp(const char* str1, const char* str2) {
    assert(str1 != nullptr && str2 != nullptr);
    // 找到首个不相等的字符
    while(*str1 != '\0' && *str2 != '\0' && *str1 == *str2) {
        str1++;
        str2++;
    }
    if(*str1 > *str2) {
        return 1;
    }
    else if(*str1 < *str2) {
        return -1;
    }
    else {
        return 0;
    }
}
```



### 实现atoi()

#### 实现要点

1. 删除前导空格
2. 检查正负号
3. 从第一个非空格字符开始转换（如果不是数字，直接返回0）
4. 将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。
5. 如果整数数超过 32 位有符号整数范围 [ $$−2^{31}$$ ,  $$2^{31} − 1$$ ] ，需要截断这个整数，使其保持在这个范围内。具体来说，小于 $$−2^{31}$$ 的整数应该被固定为 $$−2^{31}$$ ，大于 $$2^{31} − 1$$ 的整数应该被固定为 $$2^{31} − 1$$ 。

```c++
int myAtoi(string s) {
    int i = 0;
    bool negative = false;

    // 取出前导空格
    while(i < s.size() && s[i] == ' ') {
        ++i;
    }

    if(i == s.size())   return 0;

    // -号
    if(s[i] == '-') {
        negative = true;
        ++i;
    }
    // +号
    else if(s[i] == '+') {
        ++i;
    }
    // 其他字符
    else if(s[i] - '0' < 0 || s[i] - '0' > 9){
        return 0;
    }

    int sum = 0;
    // 当 s[i] 为数字
    while(i < s.size() && s[i] - '0' >= 0 && s[i] - '0' <= 9) {
        if(sum > (INT_MAX - (s[i] - '0')) / 10) {  // 防溢出
            return negative? INT_MIN : INT_MAX;
        }
        sum = sum * 10 + (s[i] - '0');
        ++i;
    }

    return negative? -sum : sum;
}
```



## 模拟加减乘除

### 不用除号的除法

例题：[29. 两数相除](https://leetcode-cn.com/problems/divide-two-integers/)

```c++
int divide(int dividend, int divisor) {
    if(dividend == INT_MIN) {
        if(divisor == 1)
            return INT_MIN;
        else if(divisor == -1)
            return INT_MAX;
    }
    if(divisor == INT_MIN) {
        if(dividend == INT_MIN)
            return 1;
        else
            return 0;
    }

    // 转换为负数计算（负数范围更大）
    bool flag = false;
    if(dividend > 0) {
        dividend = -dividend;
        flag = !flag;
    }
    if(divisor > 0) {
        divisor = -divisor;
        flag = !flag;
    }

    int result = 0;
    vector<int> candicates = {divisor};
    while(dividend - candicates.back() <= candicates.back()) {
        candicates.emplace_back(candicates.back() + candicates.back());
    }

    for(int i = candicates.size() - 1; i >= 0; --i) {
        if(dividend <= candicates[i]) {
            dividend -= candicates[i];
            result += (1 << i);
        }
    }

    return flag? -result : result;
}
```

### 快速幂

例题：[50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

- 递归版

把递归的树状图画出来分析时间复杂度

```c++
double myPow(double x, int n) {
    if(n == 0)  return 1.0;
    else if((n & 1) == 0) {
        return myPow(x * x, n / 2);
    }
    else {
        return (n > 0 ? x : 1.0 / x) * myPow(x * x, n / 2);
    }
}
```

- 迭代版

![HQM7S.png](https://s1.328888.xyz/2022/05/10/HQM7S.png)

```c++
double myPow(double x, int n) {
    if(x == 0)  return x;
    int64_t power = n;

    if(n < 0) { // 如果n < 0，求1/x的-n次方
        power *= -1;
        x = 1 / x;
    }

    double result = 1, weight = x;
    while(power != 0) {
        if((power & 1) == 1)
            result *= weight;
        weight *= weight;
        power >>= 1;
    }

    return result;
}
```



### 平方根

例题：

1. [69. x 的平方根 ](https://leetcode.cn/problems/sqrtx/)



- 二分法

从`[0, x]`开始二分查找，注意的是，当`mid*mid <= x`时，mid既有可能是解，也有可能不是。

```c++
int mySqrt(int x) {
    if(x == 1)  return 1;
    if(x == 0)  return 0;
    int left = 0, right = x, result = -1;
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if((long long) mid * mid <= x) {
            result = mid;
            left = mid + 1;
        }
        else {
            right = mid - 1;
        }
    }

    return result;
}
```

- 牛顿迭代法（求精确的需要使用该方法）

由于寻找方程零点，就是从起始点开始，一直作斜率为
$$
f'(x_i) = 2x_i
$$
的切线，然后求直线与x轴的交点，为下一个起始点。

```c++
int mySqrt(int x) {
    if (x == 0) {
        return 0;
    }

    double C = x, x0 = x;
    while (true) {
        double xi = 0.5 * (x0 + C / x0);
        if (fabs(x0 - xi) < 1e-7) {
            break;
        }
        x0 = xi;
    }
    return int(x0);
}
```



## 位运算

位运算都比较简单，主要是记住以下公式

1. 将一个数的最后一个1变为0：$$n \& (n - 1)$$
2. 获取一个数最后的1：$$n \& (-n)$$

以此可以在 $$O(log(n))$$ 解决以下题目：

1. [剑指 Offer 15. 二进制中1的个数](https://leetcode.cn/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)
2. [231. 2 的幂](https://leetcode.cn/problems/power-of-two/)（看一个数里面是不是只有一个1）
3. [输入两个数m、n，计算要改变几位二进制位才能由m得到n](https://blog.csdn.net/hansionz/article/details/82254970)（两个数取异或，然后统计1的个数）
4. 





## 计算奇奇怪怪的数



### 质数

给定整数 `n` ，返回 *所有小于非负整数 `n` 的质数的数量* 。

#### 思路

1. 从 2 开始搜索，判断每个遍历的数是否质数。
2. 空间换时间，建立一个质数列表，因为一个数 x 是质数的话，后面的 x, 2x, 3x, ...... 一定不是质数，但是 2x, 3x 前面一定已经遍历过了，所以可以从 x*x 开始遍历到 n



#### 例题

1. [204. 计数质数](https://leetcode.cn/problems/count-primes/)

```c++
// 204. 计数质数
int countPrimes(int n) {
    // 已经是质数的话，后面就不能出现质数的倍数了
    int res = 0;
    vector<bool> isPrime(n, true);
    for(int i = 2; i < n; ++i) {
        if(isPrime[i]) {
            ++res;
            if((long long)i * i < n) {  //先把 i 临时转换成 long long，否则会溢出
                for(int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
    }

    return res;
}
```



### 大数运算

#### 打印大数

例题：[剑指 Offer 17. 打印从1到最大的n位数](https://leetcode.cn/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)

输入数字 `n`，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

**思路：**

1. 模拟十进制数的加法运算
2. 全排列（回溯）

**要点：**

1. 需要考虑大数，即 n 可能超出 INT_MAX 的范围。需要用字符串表示；
2. 对字符串递增，需要模拟整数运算；
3. 输出字符串时需要先去除先导零



- **模拟加法**

```c++
bool addAndcheck(string& s) {
    bool isOverFlow = false;
    int carry = 0;
    for(int i = s.size() - 1; i >= 0; --i) {
        int cur = s[i] - '0' + carry;
        if(i == s.size() - 1)  // 加1
            ++cur;
        if(cur >= 10) {  // 产生进位
            if(i == 0)  isOverFlow = true;
            else {
                carry = 1;
                s[i] = (cur - 10) + '0';
            }
        }
        else {
            s[i] = cur + '0';
            break;
        }
    }

    return isOverFlow;
}

void printNum(string& s) {
    bool isBeginningZero = true;
    for(int i = 0; i < s.size(); ++i) {
        if(isBeginningZero && s[i] != '0') {
            isBeginningZero = false;
        }

        if(!isBeginningZero) {
            cout << s[i];
        }
    }
    cout << endl;
}

void PrintToMaxOfNdigits(int n) {
    if(n < 0)   return;
    string s(n, '0');
    while(!addAndcheck(s)) {
        printNum(s);
    }
}
```

- **排列（一眼回溯）**

```c++
void printNum(string& s) {
    bool isBeginningZero = true;
    for(int i = 0; i < s.size(); ++i) {
        if(isBeginningZero && s[i] != '0') {
            isBeginningZero = false;
        }

        if(!isBeginningZero) {
            cout << s[i];
        }
    }
    cout << endl;
}

void backtracking(string& path, int start, int length) {
    if(start == length) {
        printNum(path);
        return;
    }

    int cur = (start == 0 ? 1 : 0);
    for(int i = cur; i < 10; ++i) {
        path.push_back('0' + i);
        backtracking(path, start + 1, length);
        path.pop_back();
    }
}

void PrintToMaxOfNdigits(int n) {
    if(n < 0)   return;
    string s;
    for(int i = 1; i <= n; ++i) {
        backtracking(s, 0, i);
    }
}
```



### 判断一个字符串是否表示数值

**例题：**[剑指 Offer 20. 表示数值的字符串](https://leetcode.cn/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)

```c++
class Solution {
public:
    bool isNumber(string s) {
        // 一个数由：有符号整数部分 + '.' + 无符号整数部分 + 'e/E' + 有符号整数 组成
        int index = 0;
        // 跳过前导空格
        while(index < s.size() && s[index] == ' ') {
            ++index;
        }
        bool isValid = scanInt(s, index);
        if(s[index] == '.') {
            ++index;
            // '.'前后都可以没有数字
            // 所以用 |
            isValid |= scanUInt(s, index);
        }
        if(s[index] == 'e' || s[index] == 'E') { 
            ++index;
            // E前面和后面都有数字才有效
            // 所以用 &
            isValid &= scanInt(s, index);
        }

        // 跳过后面的空格
        while(index < s.size() && s[index] == ' ') {
            ++index;
        }

        return index == s.size() && isValid;
    }

private:
    bool scanInt(const string& s, int& index) {
        if(index >= s.size())   return false;

        if(s[index] == '+' || s[index] == '-') {
            ++index;
        }

        return scanUInt(s, index);
    }

    bool scanUInt(const string& s, int& index) {
        if(index >= s.size())   return false;

        int start = index;
        while(index < s.size() && s[index] >= '0' && s[index] <= '9') {
            ++index;
        }

        return index > start;
    }
    
};
```



# 基本数据结构实现

## 栈相关

### 最小栈的实现

基本功能与栈相同，但是多了一个`getMin()`获取最小值

**例题：**[155. 最小栈](https://leetcode.cn/problems/min-stack/)

**基本思路：**

- 两个栈（一个实现栈的基本功能，一个存放最小值）
- 一个栈
  - 栈内放的是 `pair<int, int>` 第一个是放入的元素，第二个是最小值
  - 栈内放的是 `当前元素 - 最小值` 
- 直接使用链表，链表内存放当前元素和最小值



```c++
// 栈内存放插值
class MinStack {
public:
    stack<long long> stk;
    long long diff;
    long long min;
    bool block=true;   //是否是在栈为空的时候进行push
    MinStack() {
    }
    
    void push(int val) {
        if(block&&val>0){
            min=INT_MAX;       //栈为空且val>0时，min的初始值为INT_MAX
            block=false;       //上锁
        }
        else if(block&&val<=0){
            min=0;
            block=false;
        }
        diff=(long long)val-min;
        stk.push(diff);
        stk.push(val);
        if(diff<0)          //若diff<0,则说明push的值是当前最小值
            min=val;
    }
    
    void pop() {
        int top=stk.top();   //栈顶元素的值
        stk.pop();
        int d=stk.top();    //属于栈顶元素的diff
        stk.pop();
        min=(long long)top-d;    //更新当前最小值(若pop的元素不是最小值，其实min是不变的)
        if(stk.empty()) block=true;    //若出栈导致栈空，则解锁
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return min;
    }
};
```



```java
// 链表
class MinStack {
    class Node{
        int value;
        int min;
        Node next;

        Node(int x, int min){
            this.value=x;
            this.min=min;
            next = null;
        }
    }
    Node head;
    //每次加入的节点放到头部
    public void push(int x) {
        if(null==head){
            head = new Node(x,x);
        }else{
            //当前值和之前头结点的最小值较小的做为当前的 min
            Node n = new Node(x, Math.min(x,head.min));
            n.next=head;
            head=n;
        }
    }

    public void pop() {
        if(head!=null)
            head =head.next;
    }

    public int top() {
        if(head!=null)
            return head.value;
        return -1;
    }

    public int getMin() {
        if(null!=head)
            return head.min;
        return -1;
    }
}
```



# 智力题

**1、101硬币，里面有1假币，用2次天平，判断假币更重还是更轻（阿里云）**

第一次天平两边各放50个，会出现两种情况：
1. 平衡：那么剩下的肯定是假币，将它与真币对比即可
2. 不平衡：将其中一堆50个的分为两堆25的（如拿轻的），放天平两边，会出现两种情况：
    * 平衡：则假的在另一堆，假币更重
    * 不平衡：假的就在这堆，假币更轻

2、







# 场景题汇总

