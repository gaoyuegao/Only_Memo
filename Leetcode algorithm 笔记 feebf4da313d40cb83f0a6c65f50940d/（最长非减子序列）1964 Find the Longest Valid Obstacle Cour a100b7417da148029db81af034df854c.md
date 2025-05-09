# （最长非减子序列）1964. Find the Longest Valid Obstacle Course at Each Position

**Hard**

You want to build some obstacle courses. You are given a **0-indexed** integer array `obstacles` of length `n`, where `obstacles[i]` describes the height of the `ith` obstacle.

For every index `i` between `0` and `n - 1` (**inclusive**), find the length of the **longest obstacle course** in `obstacles` such that:

- You choose any number of obstacles between `0` and `i`**inclusive**.
- You must include the `ith` obstacle in the course.
- You must put the chosen obstacles in the **same order** as they appear in `obstacles`.
- Every obstacle (except the first) is **taller** than or the **same height** as the obstacle immediately before it.

Return *an array* `ans` *of length* `n`, *where* `ans[i]` *is the length of the **longest obstacle course** for index* `i` *as described above*.

```python
def longestObstacleCourseAtEachPosition(self, nums: List[int]) -> List[int]:
        lis = []
        for i, x in enumerate(nums):
            if len(lis) == 0 or lis[-1] <= x:  # Append to LIS if new element is >= last element in LIS
                lis.append(x)
                nums[i] = len(lis)
            else:
                idx = bisect_right(lis, x)  # Find the index of the smallest number > x
                lis[idx] = x  # Replace that number with x
                nums[i] = idx + 1
        return nums
```