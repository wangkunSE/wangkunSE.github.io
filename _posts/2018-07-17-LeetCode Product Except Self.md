## LeetCode：Product of Array Except Self

#### 题目：

> 给一个长度大于1的数组，返回一个数组使得`output[i]` 等于nums中元素除了自身（`nums[i]`）的乘积。要`O(n)`的复杂度。

#### 例子:

> ```java
> Input:  [1,2,3,4]
> Output: [24,12,8,6]
> ```

#### 思路：

> 算出一个总的乘积，当前位置就是总的乘积除以当前的值。

#### 代码：

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        if( nums == null || nums.length < 2){
            return nums;
        }
        int length = nums.length;
        long total = 1L;
        int zeroCount = 0;
        for(int i = 0; i < length; i++){
            if(nums[i] != 0){
                total = nums[i] * total;
            }else{
                zeroCount ++;
            }
        }
        if(zeroCount > 1){
            Arrays.fill(nums,0);
            return nums;
        }
        
        for(int i = 0; i < length; i++){
            int temp = nums[i];
            if(temp!= 0){
                if(zeroCount > 0){
                    nums[i] = 0;
                }else{
                    nums[i] = (int)(total / temp);
                }
            }else{
                nums[i] = (int)total;
            }
        }
        return nums;
    }
}
```

#### 增加条件：

> 当不能用除法的时候。这时就需要计算一个分界，左边和右边的乘积，这样就能刨除自己算出当前的值。

##### 代码:

```java
public int[] productExceptSelf(int[] nums) {
    if (nums == null || nums.length < 2) {
        return nums;
    }
    int length = nums.length;
    int[] res = new int[length];
    res[0] = 1;
    for (int i = 1; i < length; i++) {
        res[i] = res[i - 1] * nums[i - 1];
    }
    int right = 1;
    for (int i = length - 1; i >= 0; i--) {
        res[i] = right * res[i];
        right = right * nums[i];
    }
    return res;
}
```

