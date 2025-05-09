# (倒水问题最大公约数)365. Water and Jug Problem

**Medium**

You are given two jugs with capacities `jug1Capacity` and `jug2Capacity` liters. There is an infinite amount of water supply available. Determine whether it is possible to measure exactly `targetCapacity` liters using these two jugs.

If `targetCapacity` liters of water are measurable, you must have `targetCapacity` liters of water contained **within one or both buckets** by the end.

```python
def canMeasureWater(self, jug1Capacity: int, jug2Capacity: int, targetCapacity: int) -> bool:
        if jug1Capacity+jug2Capacity<targetCapacity:
            return False
        def gcd1(x,y):
            while y!=0:
                y,x=x%y,y
            return x
        return targetCapacity==0 or targetCapacity%gcd1(jug1Capacity,jug2Capacity)==0
```