## LeetCode:[Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

### 题目：

> 给一个排好序的数组，其中有重复元素，要求用O(1)的空间复杂度修改输入的数组。并返回数组最后有效长度。

### 例子：

> ```java
> Example 1:
> 
> Given nums = [1,1,1,2,2,3],
> 
> Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.
> 
> It doesn't matter what you leave beyond the returned length.
> Example 2:
> 
> Given nums = [0,0,1,1,1,1,2,3,3],
> 
> Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.
> 
> 超出length的数值是多少是无关紧要的。
> ```

### 思路：

> 一开始就想到一个很容易操作的思路，就是遍历数组，用count来记录遇到当前值的次数，少于2就把长度和count值递增，然后遇到下一个不一样的值时若count大于2则需要移动一下数组的值。

### 代码:

> 这个算法虽然可以解决这个题，但是效率有些低，因为需要移动数组的值所以最坏复杂度为O(n^2)。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums == null || nums.length < 1){
            return 0;
        }
        
        int result = 1;
        int count = 0;
        int temp = nums[0];
        int length = nums.length;
        
        for(int i = 1; i < length; i++){
            if(temp == nums[i]){
                if(count < 1){
                    count++;
                    result++;
                }else{
                    count++;
                }
            }else{
                if(count > 1){
                    int move = count - 1;
                    for(int j = i - move; j < length - move ;j++){
                        nums[j] = nums[j+move];
                    }
                    i -= move;
                    length -= move;
                }
                count = 0;
                result ++;
            }
            temp = nums[i];
        }
        
        return result;
    }
}
```

### 思路二：

> 其实移动的操作完全可以用一个指针来代替，一个遍历指针，一个数组有效长度指针。代码如下

### 代码二：

> 可以看到因为移动不需要额外操作，所以复杂度为O(n)，提高了不少。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums == null || nums.length < 1){
            return 0;
        }
        int i =1, j = 1;
        int cnt = 1;
        int k = 2;
        int n = nums.length;
        while( j < n){
            if(nums[j] != nums[j-1]){
                cnt = 1;
                nums[i++] = nums[j];
            }else{
                if(cnt < k){
                    cnt++;
                    nums[i++] = nums[j];
                }else{
                    cnt++;
                }
            }
            j++;
        }
        
        return i;
    }
}
```

