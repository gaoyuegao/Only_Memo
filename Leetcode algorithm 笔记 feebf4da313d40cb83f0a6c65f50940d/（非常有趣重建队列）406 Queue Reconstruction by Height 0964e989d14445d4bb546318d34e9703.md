# （非常有趣重建队列）406. Queue Reconstruction by Height

**Medium**

You are given an array of people, `people`, which are the attributes of some people in a queue (not necessarily in order). Each `people[i] = [hi, ki]` represents the `ith` person of height `hi` with **exactly** `ki` other people in front who have a height greater than or equal to `hi`.

Reconstruct and return *the queue that is represented by the input array* `people`. The returned queue should be formatted as an array `queue`, where `queue[j] = [hj, kj]` is the attributes of the `jth` person in the queue (`queue[0]` is the person at the front of the queue).

```python
def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        people.sort(key = lambda x: (-x[0], x[1]))
        print(people)
        output = []
        for p in people:
            output.insert(p[1], p)
        return output
    #按从大到小，index从小到大的顺序排列
    #每次insert插入进去的时候，因为现在的都是比当前要插入数字大的
    #所以插到她的index的位置就可以，至于所有小于她的插到哪里都无所谓
```