# (bit manipulation addition)371. Sum of Two Integers

**Medium**

Given two integers `a` and `b`, return *the sum of the two integers without using the operators* `+` *and* `-`.

把所有应该是一的地方换成一

第一我们用xor记录所有应该是一的地方，但现在是肯定小于或等于和的，然后用and 操作记录下应该进位的地方，再次异或，把该是一的换成一，carry的位数是越来越少的

```python
def getSum(self, a: int, b: int) -> int:
        x, y = abs(a), abs(b)
        if x<y:
            return self.getSum(b,a)
        sign = 1 if a>0 else -1
        if a*b>=0:
            while y:
                x,y=x^y,(x&y)<<1
        else:
            while y:
                x,y=x^y,(~x&y)<<1
        return x*sign
```