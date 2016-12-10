---
layout:     post
title:      Solution of Russian Doll Envelopes
discription: Leetcode Russian Doll Envelopes problem,it's all about how to think and solve it
date:       2016-12-10 23:40:00
catalog:    true
tags:       [Java, Leetcode, 算法, DP问题, ]
---

## Solution of Russian Doll Envelopes

> [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/) 来自Leetcode上一道经典的DP问题，在仔细研究这道题一整天后，决定写下这篇博文记录下整个思路。 首先看到这个问题第一反应就是DP问题，仔细读题后发现跟之前见过的求最长递增子序列有异曲同工之妙，所有先研究了一下最长递增子序列的求法

### 求最长递增子序列

#### 1.O(n^2)解法
> 考虑到是求最长递增子序列，首先想到的是求每个元素结尾的最长递增子序列，然后比较取出最长的。这就需要一个长度为N的辅助数组，每个元素对应子序列中以其结尾的最长子序列的长度

假设输入序列为：`21645274`

|2|1|6|4|5|2|7|4|
|-|
|||||||||

最长子序列即为：`1457`，长度为`4`，那么使用辅助数组，则辅助数组的取值为：

|1|1|2|2|3|2|4|3|
|-|
|||||||||

```
 public static int subSequence(int[] inputArray){
     //endMaxArray记录以每一个元素结尾的最长子序列长度
     int[] endMaxArray = new int[inputArray.length];
     int i,j,indexMax,globalMax = 0;
     for (i = 0; i < inputArray.length; i++){
         indexMax = 1;
         for (j = 0; j < i; j++){
             if (inputArray[i] > inputArray[j]){
                 indexMax = Math.max(indexMax, endMaxArray[j] + 1);
             }
         }
         endMaxArray[i] = indexMax;
         globalMax = Math.max(globalMax,indexMax);
     }
     return globalMax;
 }
```

#### 2.O(N*logN)解法
> 考虑到时间复杂度较高，继续优化算法，想到可以维护一个最长递增子序列的最大末尾，这样下一个元素只需要考虑在递增序列中找到第一个比其大的元素，插入即构成以其结尾的最长递增子序列

同样以输入序列：`21645274`为例
1.第一个元素2直接入到有效区，此时最长递增子序列为：`2`

|2|1|6|4|5|2|7|4| 
|-|
|2||||||||

2.遍历到1时，由于1小于最长子序列的最大末尾：`2`，则将1替换有效区中第一个比它大的数，即为2，替换后：

|2|1|6|4|5|2|7|4| 
|-|
|1||||||||

则此时有效区长度为1，最长递增子序列为：`1`

3.遍历到6时，由于6大于最大末尾：`1`，则6直接入到有效区作为最大末尾

|2|1|6|4|5|2|7|4| 
|-|
|1|6|||||||

此时有效区长度为2，最长递增子序列为：`16`

4.遍历到4时，由于4小于最大末尾：`6`，则将4替换有效区中第一个比它大的数，即为6，替换后：

|2|1|6|4|5|2|7|4| 
|-|
|1|4|||||||

此时有效区长度为2，最长递增子序列为：`14`

5.遍历到5时，由于5大于最大末尾：`4`，则5直接入到有效区作为最大末尾

|2|1|6|4|5|2|7|4| 
|-|
|1|4|5||||||

此时有效区长度为3，最长递增子序列为：`145`

6.遍历到2时，由于2小于最大末尾：`5`，则将2替换有效区中第一个比它大的数，即为4，替换后：

|2|1|6|4|5|2|7|4| 
|-|
|1|2|5||||||

此时有效区长度为3，**注意：**此时最长递增子序列仍然是：`145`，用2替换4的位置表示以2结尾的最长递增子序列为：`12`，但这并不会影响以5结尾的最长递增子序列（仍为：`145`），同时最长递增子序列的长度为有效区的长度：`3`

7.遍历到7时，由于7大于最大末尾：`5`，则7直接入到有效区作为最大末尾

|2|1|6|4|5|2|7|4| 
|-|
|1|2|5|7|||||

此时有效区长度为4，最长递增子序列为：`1457`，最长递增子序列的长度为有效区的长度：`4`

8.遍历到4时，由于4小于最大末尾：`7`，则将2替换有效区中第一个比它大的数，即为5，替换后：

|2|1|6|4|5|2|7|4| 
|-|
|1|2|4|7|||||

此时有效区长度为4，**注意：**此时最长递增子序列仍然是：`1457`，同时最长递增子序列的长度为有效区的长度：`4`

这样一轮遍历结束后，有效区的长度即为最长递增子序列的长度，查找算法使用二分查找，则时间复杂度可以到O(N*logN)，实际上由于有效区的长度不为N，时间复杂度比O(N*logN)略低

```
public static int subSequence(int[] arr){
   //endMaxArray记录以每一个元素结尾的最长子序列长度
   int[] l = new int[arr.length];
   int i,j,indexMax,globalMax = 0;
   int index = 0;
   l[0] = arr[0];
   for (i = 1; i < arr.length; i++){
       if (arr[i] > l[index]){
           l[++index] = arr[i];
       }else {
           int firstMaxPos = findFirstLargerIndex(l,index,arr[i]);
           l[firstMaxPos] = arr[i];
       }
   }
   return index + 1;
}

//查找第一个比target大的数
private static int findFirstLargerIndex(int[] arr, int high, int target){
   int low = 0,middle,aHigh = high;
   while (low <= high){
       middle = (low + high) >> 1;
       if (arr[middle] > target){
           high = middle - 1;
       }else if (arr[middle] < target){
           low = middle + 1;
       }else {
           return middle;
       }
   }
   return Math.min(low,aHigh);
}
```

### Russian Doll Envelopes

> 原题如下：

You have a number of envelopes with widths and heights given as a pair of integers `(w, h)`. One envelope can fit into another if and only if both the width and height of one envelope is greater than the width and height of the other envelope.

What is the maximum number of envelopes can you Russian doll? (put one inside other)

**Example:**
Given envelopes = `[[5,4],[6,4],[6,7],[2,3]]`, the maximum number of envelopes you can Russian doll is 3 ([2,3] => [5,4] => [6,7]).

> 那么现在回到Russian Doll Envelopes问题上来，此时的输入从一维到了二维，同时不要求按输入顺序，那么第一反应就是想到要先排序

1.按w升序，h升序排列
这是很容易想到的排序方法，按这种方法排序后，有一个问题，即：[5,4]和[4,6]的大小。我们无法判断这两个信封哪个更大，因为任何一个都装不下另外一个，那么按这种排序方法排序过后，后面的步骤就进行不下去了

2.按w升序，h降序排列
在按w升序排列后，我们后面取到的信封在w这个维度上是递增的，后面信封的w肯定不会比前面的小，那么此时如果选择按h维度插入到前面第一个比此信封的h大的位置，那么必然可以保证此信封w和h要比前面的都大，也就是最长递增子序列的最大末尾，所以这里如果按w升序，h降序，则将二维问题转化为一维问题了，步骤同上（仅考虑h）

```
public static int maxEnvelopes(int[][] envelopes) {
    if (envelopes.length == 0){
        return 0;
    }
    //按w升序，h降序排列
    Arrays.sort(envelopes, new Comparator<int[]>() {
        public int compare(int[] o1, int[] o2) {
            if (o1[0] == o2[0]){
                return o2[1] - o1[1];
            }else {
                return o1[0] - o2[0];
            }
        }
    });
    int[] l = new int[envelopes.length];
    int index = 0;
    //辅助数组只考虑h维度
    l[index] = envelopes[0][1];
    for (int i = 1; i < envelopes.length; i++){
        if (envelopes[i][1] > l[index]){
            l[++index] = envelopes[i][1];
        }else {
            int firstMaxPos = findFirstLargerIndex(l,index,envelopes[i][1]);
            l[firstMaxPos] = envelopes[i][1];
        }
    }
    return ++index;
}

private static int findFirstLargerIndex(int[] arr, int high, int target){
    ...
}
```

