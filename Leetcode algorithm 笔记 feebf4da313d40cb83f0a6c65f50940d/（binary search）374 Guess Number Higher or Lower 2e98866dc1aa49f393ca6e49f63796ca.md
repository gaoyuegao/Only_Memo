# （binary search）374. Guess Number Higher or Lower

**Easy**

We are playing the Guess Game. The game is as follows:

I pick a number from `1` to `n`. You have to guess which number I picked.

Every time you guess wrong, I will tell you whether the number I picked is higher or lower than your guess.

You call a pre-defined API `int guess(int num)`, which returns 3 possible results:

- `1`: The number I picked is lower than your guess (i.e. `pick < num`).
- `1`: The number I picked is higher than your guess (i.e. `pick > num`).
- `0`: The number I picked is equal to your guess (i.e. `pick == num`).

Return *the number that I picked*.

```python
def guessNumber(self, n: int) -> int:
        low=0
        while low<=n:
            m=low+(n-low)//2
            t=guess(m)
            if t==-1:
                n=m-1
            elif t==1:
                low=m+1
            else:
                return m
        return None
```