## LeetCode:[Merge Intervals](https://leetcode.com/problems/merge-intervals/)

#### 题目：

> 给一个集合的区间，把有重叠的区间合并起来

#### 例子：

> ```java
> Example 1:
> 
> Input: [[1,3],[2,6],[8,10],[15,18]]
> Output: [[1,6],[8,10],[15,18]]
> Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
> Example 2:
> 
> Input: [[1,4],[4,5]]
> Output: [[1,5]]
> Explanation: Intervals [1,4] and [4,5] are considerred overlapping.
> ```

#### 思路：

> 一开始我的思路是把start排序一遍，这样O(n)的复杂度就可以搞定，但是越写越恶心，因为边界情况很多。

#### 代码:

```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
class Solution {
    public List<Interval> merge(List<Interval> intervals) {
        
         List<Interval> result = new ArrayList<>();
            if (intervals == null || intervals.size() < 1) {
                return result;
            }

            Object[] intervalArr = intervals.toArray();
            Arrays.sort(intervalArr, new Comparator<Object>() {
                @Override
                public int compare(Object o1, Object o2) {
                    Interval interval1 = (Interval) o1;
                    Interval interval2 = (Interval) o2;
                    return Integer.compare(interval1.start, interval2.start);
                }
            });
             for (int i = 1; i < intervalArr.length; i++) {
                Interval ibefore = (Interval) intervalArr[i - 1];
                Interval intervalI = (Interval) intervalArr[i];
                if (intervalI.start <= ibefore.end) {
                    Interval newInter = new Interval(ibefore.start, intervalI.end > ibefore.end ? intervalI.end : ibefore.end);
                    intervalArr[i] = newInter;
                    addInterval(result, newInter);
                } else {
                    addInterval(result, ibefore);
                }
            }
            addInterval(result,(Interval)intervalArr[intervalArr.length-1]);
            return result;
        }

        private void addInterval(List<Interval> result, Interval interval) {
            Interval intervalTemp = null;
            if (result.size() > 0) {
                intervalTemp = result.get(result.size() - 1);
                if (intervalTemp.start == interval.start && intervalTemp.end < interval.end) {
                    result.remove(result.size() - 1);
                }
            }
            if (intervalTemp == null || intervalTemp.start != interval.start || intervalTemp.end != interval.end) {
                result.add(interval);
            }
        }
}
```

#### 解法二：

> 将start，end分别排序，然后重新划区间，这个思路很清晰，代码看着也很舒服。

```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
class Solution {
    public List<Interval> merge(List<Interval> intervals) {
        List<Interval> result = new ArrayList<>();
        if(intervals == null || intervals.size()<1){
            return result;
        }
        
        int length = intervals.size();
        int[] start = new int[length];
        int[] end = new int[length];
        for(int i = 0; i<length; i++){
            start[i] = intervals.get(i).start;
            end[i] = intervals.get(i).end;
        }
        
        Arrays.sort(start);
        Arrays.sort(end);
        
        for(int i=0,j=0; i<length; i++){
            if(i == length-1 || start[i+1] > end[i]){
                result.add(new Interval(start[j],end[i]));
                j = i+1;
            }
        }
        return result;
    }
}
```

