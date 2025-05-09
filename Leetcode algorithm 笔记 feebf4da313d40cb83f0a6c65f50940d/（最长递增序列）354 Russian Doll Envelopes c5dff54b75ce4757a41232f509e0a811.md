# （最长递增序列）354. Russian Doll Envelopes

这个题就是先排序，然后按照另外一个找到一个最长的递增序列

但是我们只需要这个最长递增序列的长度

最长递增序列算法可以通过建立一个数列存储这个递增序列

不断的把当前循环到的数字插入到能插入的最左边的点

使用python自带的函数bisect（这个函数使用二分搜索，搜索到当前数字最左边的插入位置）

**Hard**

You are given a 2D array of integers `envelopes` where `envelopes[i] = [wi, hi]` represents the width and the height of an envelope.

One envelope can fit into another if and only if both the width and height of one envelope are greater than the other envelope's width and height.

Return *the maximum number of envelopes you can Russian doll (i.e., put one inside the other)*.

**Note:** You cannot rotate an envelope.

```python
def maxEnvelopes(self, arr: List[List[int]]) -> int:
        # sort increasing in first dimension and decreasing on second
        arr.sort(key=lambda x: (x[0], -x[1]))
        print(arr)
        def lis(nums):
            dp = []
            
            for i in range(len(nums)):
                
                idx = bisect_left(dp, nums[i])
                print(idx)
                if idx == len(dp):
                    dp.append(nums[i])
                else:
                    dp[idx] = nums[i]
            return len(dp)
        # extract the second dimension and run the LIS
        return lis([i[1] for i in arr])
```

**646. Maximum Length of Pair Chain**

**Medium**

You are given an array of `n` pairs `pairs` where `pairs[i] = [lefti, righti]` and `lefti < righti`.

A pair `p2 = [c, d]` **follows** a pair `p1 = [a, b]` if `b < c`. A **chain** of pairs can be formed in this fashion.

Return *the length longest chain which can be formed*.

You do not need to use up all the given intervals. You can select pairs in any order.

```python
def findLongestChain(self, pairs: List[List[int]]) -> int:
        pairs.sort(key=lambda x: x[0])
        dp = [1] * len(pairs)

        for j in range(len(pairs)):
            for i in range(j):
                if pairs[i][1] < pairs[j][0]:
                    dp[j] = max(dp[j], dp[i] + 1)

        return max(dp)

```

我们需要找到最长的并且存储所有的路径

## 673. Number of Longest Increasing Subsequence

**Medium**

Given an integer array `nums`, return *the number of longest increasing subsequences.*

**Notice** that the sequence has to be **strictly** increasing.

The idea of my solution is to use so-called Patience sort: [https://en.wikipedia.org/wiki/Patience_sorting](https://en.wikipedia.org/wiki/Patience_sorting)

The idea is to keep several `decks`, where numbers in each deck are decreasing. Also when we see new card, we need to put it to the end of the leftest possible deck. Also we have `paths`: corresponing number of LIS, ending with given `num`. That is in `paths[0]` we keep number of LIS with length `1`, in `paths[k]` we keep number of LIS with length `k+1`. (we keep cumulative sums) Also we keep `ends_decks` list to have quick access to end of our decks.

**Property**: It can be proved, that each LIS can be formed as choosing not more than one number from each deck and choosing them, looking at decks from left to right.

Imagine, that we have `nums = [1,3,5,4,7,10,8,2,8]`, then we have the following `decks` and `paths` step by step:

`decks = [[-1]] paths = [[0, 1]]decks = [[-1], [-3]] paths = [[0, 1], [0, 1]]decks = [[-1], [-3], [-5]] paths = [[0, 1], [0, 1], [0, 1]]decks = [[-1], [-3], [-5, -4]] paths = [[0, 1], [0, 1], [0, 1, 2]]decks = [[-1], [-3], [-5, -4], [-7]] paths = [[0, 1], [0, 1], [0, 1, 2], [0, 2]]decks = [[-1], [-3], [-5, -4], [-7], [-10]] paths = [[0, 1], [0, 1], [0, 1, 2], [0, 2], [0, 2]]decks = [[-1], [-3], [-5, -4], [-7], [-10, -8]] paths = [[0, 1], [0, 1], [0, 1, 2], [0, 2], [0, 2, 4]]decks = [[-1], [-3, -2], [-5, -4], [-7], [-10, -8]] paths = [[0, 1], [0, 1, 2], [0, 1, 2], [0, 2], [0, 2, 4]]decks = [[-1], [-3, -2], [-5, -4], [-7], [-10, -8, -8]] paths = [[0, 1], [0, 1, 2], [0, 1, 2], [0, 2], [0, 2, 4, 6]]`

We use negative numbers, so each deck is sorted in increasing way instead of decreasing.

When we see new `num`, then we first find `deck_idx`: number of deck we need to put this `num`. Now, we want to find number of LIS, ending with this `num`: for this we need to look at previous deck and find the place of `num` inside this deck: here we use our **property**, we update our `n_path`.

Now, we need to decide where we put this number:

1. If our `deck_idx` is equal to `len(decks)`, it means, that we need to create new `deck`: we create new deck with one element, update `ends_decks` and also append `n_paths` to our `paths`.
2. In opposite case, we need to add `nums` to the end of corresponding deck, again update `ends_decks` and update our `pathcs[deck_idx]`: it will consist of two parts: `n_paths`: number of paths, such that previous element is from previous or before decks. Also we have `paths[deck_idx][-1]`, because we keep cumulative sums inside.

Finally, we return `paths[-1][-1]`, it is number of LIS with the biggest length.

**Complexity** is `O(n log n)`, because for each new `num` we process it in `O(log n)`. Space complexity is `O(n)`. When I run it, i have times, I have times from 60ms to 72ms, where `60`ms beats 100% of python solutions.

```python
def findNumberOfLIS(self, nums):
        if not nums: return 0
    #首先如果我们只需要最长的，我们用一个数组就可以
    #但是如果我么全部需要就存储所有的decks
    #然后用另外一个数组存储路径，到这个长度的有多少个
    #有两种情况一种我们需要一个新的decks，没有找到合适的
    #另外一种是我们把当前的数放在已经有的地方就可以
    
        decks, ends_decks, paths = [], [], []
        for num in nums:
            deck_idx = bisect.bisect_left(ends_decks, num)
            n_paths = 1
            if deck_idx > 0:
                l = bisect.bisect(decks[deck_idx-1], -num)
                n_paths = paths[deck_idx-1][-1] - paths[deck_idx-1][l]
                
            if deck_idx == len(decks):
                decks.append([-num])
                ends_decks.append(num)
                paths.append([0,n_paths])
            else:
                decks[deck_idx].append(-num)
                ends_decks[deck_idx] = num
                paths[deck_idx].append(n_paths + paths[deck_idx][-1])
        #paths 记录的是到某一个deck的个数，一共有多少个deck就有多长的path
        
        print(paths)
        return paths[-1][-1]
```