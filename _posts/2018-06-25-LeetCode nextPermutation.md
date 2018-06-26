# LeetCode: nextPermutation

#### 题目：

> 大意就是找出以当前数组从左至右为一个数字的下一个大的数字的数组组合，只能用常量的空间。若已经为最大数，则返回递增数组。

#### 例子：

`1,2,3` → `1,3,2`
`3,2,1` → `1,2,3`
`1,1,5` → `1,5,1`

 

#### 思路：

> 其实这个题我一开始想的思路是从后往前找第一个比前一个值大的数，该位置就称为index好了，要满足nums[index]>nums[index-1]，交换就OK，但是仔细想了下不对，应该是找到那个位置，然后从后往前找出最小的那个比nums[index-1]大的值，交换后排序index-1后的数组就OK了。

#### 代码：

```java
class Solution {
    public void nextPermutation(int[] nums) {
            if(nums == null || nums.length <= 1){
                return;
            }
            int length = nums.length;
            int tail = Integer.MAX_VALUE;
            int right = length -1;
            int rightMin = Integer.MAX_VALUE;
            while(right > 0){
                if(nums[right] > nums[right -1] ){
                    break;
                }
                right--;
            }
            int index = length -1;
            while(index >= right && right > 0){
                if(rightMin>nums[index] && nums[index] > nums[right -1]){
                    rightMin = nums[index];
                    tail = index;
                }
                index --;
            }

            if (right > 0) {
                swap(nums, tail, right - 1);
                reverseSort(nums, right, length);
            } else {
                reverseSort(nums,0,length);
            }
        }
        private void reverseSort(int[] nums,int start,int end){
            for (int i = 0; i < (end-start) / 2; i++) {
                swap(nums, start+i, end - i - 1);
            }
        }

        private void swap(int[] arr,int i,int j){
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
}
```

