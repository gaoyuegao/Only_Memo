# (Binary index tree)307. Range Sum Query - Mutable

**Medium**

Given an integer array `nums`, handle multiple queries of the following types:

1. **Update** the value of an element in `nums`.
2. Calculate the **sum** of the elements of `nums` between indices `left` and `right`**inclusive** where `left <= right`.

Implement the `NumArray` class:

- `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
- `void update(int index, int val)` **Updates** the value of `nums[index]` to be `val`.
- `int sumRange(int left, int right)` Returns the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** (i.e. `nums[left] + nums[left + 1] + ... + nums[right]`).

```python
def __init__(self, nums):
        self.n = len(nums)
        self.a, self.c = nums, [0] * (self.n + 1)
        for i in range(self.n):
            k = i + 1
            while k <= self.n:
                self.c[k] += nums[i]
                k += (k & -k)

    def update(self, i, val):
        diff, self.a[i] = val - self.a[i], val
        i += 1
        while i <= self.n:
            self.c[i] += diff
            i += (i & -i)

    def sumRange(self, i, j):
        res, j = 0, j + 1
        while j:
            res += self.c[j]
            j -= (j & -j)
        while i:
            res -= self.c[i]
            i -= (i & -i)
        return res
```

Segment Tree can be broken down to the three following steps:

1. Pre-processing step which builds the segment tree from a given array.
2. Update the segment tree when an element is modified.
3. Calculate the Range Sum Query using the segment tree.

### **1. Build segment tree**

We will use a very effective bottom-up approach to build segment tree. We already know from the above that if some node p*p* holds the sum of [i…j][*i*…*j*] range, its left and right children hold the sum for range [i…i+j2][*i*…2*i*+*j*] and [i+j2+1,j][2*i*+*j*+1,*j*] respectively.

Therefore to find the sum of node p*p*, we need to calculate the sum of its right and left child in advance.

We begin from the leaves, initialize them with input array elements a[0,1,…,n−1]*a*[0,1,…,*n*−1]. Then we move upward to the higher level to calculate the parents' sum till we get to the root of the segment tree.

**Complexity Analysis**

- Time complexity : O(n)*O*(*n*)
    
    Time complexity is  O(n)*O*(*n*), because we calculate the sum of one node during each iteration of the for loop. There are approximately 2n2*n* nodes in a segment tree.
    
    This could be proved in the following way: Segmented tree for array with n*n*elements has n*n* leaves (the array elements itself). The number of nodes in each level is half the number in the level below.
    
    So if we sum the number by level we will get:
    
    n+n/2+n/4+n/8+…+1≈2n*n*+*n*/2+*n*/4+*n*/8+…+1≈2*n*
    
- Space complexity : O(n)*O*(*n*).
    
    We used 2n2*n* extra space to store the segment tree.
    
    ```java
    
    int[] tree;
    int n;
    public NumArray(int[] nums) {
        if (nums.length > 0) {
            n = nums.length;
            tree = new int[n * 2];
            buildTree(nums);
        }
    }
    private void buildTree(int[] nums) {
        for (int i = n, j = 0;  i < 2 * n; i++,  j++)
            tree[i] = nums[j];
        for (int i = n - 1; i > 0; --i)
            tree[i] = tree[i * 2] + tree[i * 2 + 1];
    }
    ```
    

### **2. Update segment tree**

When we update the array at some index i*i* we need to rebuild the segment tree, because there are tree nodes which contain the sum of the modified element. Again we will use a bottom-up approach. We update the leaf node that stores a[i]*a*[*i*]. From there we will follow the path up to the root updating the value of each parent as a sum of its children values.

```java

void update(int pos, int val) {
    pos += n;
    tree[pos] = val;
    while (pos > 0) {
        int left = pos;
        int right = pos;
        if (pos % 2 == 0) {
            right = pos + 1;
        } else {
            left = pos - 1;
        }
        // parent is updated after child is updated
        tree[pos / 2] = tree[left] + tree[right];
        pos /= 2;
    }
}
```

**Complexity Analysis**

- Time complexity : O(log⁡n)*O*(log*n*).
    
    Algorithm has O(log⁡n)*O*(log*n*) time complexity, because there are a few tree nodes with range that include  i*i*th array element, one on each level. There are log⁡(n)log(*n*) levels.
    
- Space complexity : O(1)*O*(1).

### **3. Range Sum Query**

We can find range sum query  [L,R][*L*,*R*] using segment tree in the following way:

Algorithm hold loop invariant:

l≤r*l*≤*r* and sum of [L…l][*L*…*l*] and [r…R][*r*…*R*] has been calculated, where l*l* and r*r* are the left and right boundary of calculated sum. Initially we set l*l* with left leaf L*L* and r*r* with right leaf R*R*. Range [l,r][*l*,*r*] shrinks on each iteration till range borders meets after approximately log⁡nlog*n* iterations of the algorithm

- Loop till l≤r*l*≤*r*
    - Check if l*l* is right child of its parent P*P*
        - l*l* is right child of P*P*. Then P*P* contains sum of range of l*l* and another child which is outside the range [l,r][*l*,*r*] and we don't need parent P*P*sum. Add l*l* to sum*sum* without its parent P*P* and set l*l* to point to the right of P*P* on the upper level.
        - l*l* is not right child of P*P*. Then parent P*P* contains sum of range which lies in [l,r][*l*,*r*]. Add P*P* to sum*sum* and set l*l* to point to the parent of P*P*
    - Check if r*r* is left child of its parent P*P*
        - r*r* is left child of P*P*. Then P*P* contains sum of range of r*r* and another child which is outside the range [l,r][*l*,*r*] and we don't need parent P*P*sum. Add r*r* to sum*sum* without its parent P*P* and set r*r* to point to the left of P*P* on the upper level.
        - r*r* is not left child of P*P*. Then parent P*P* contains sum of range which lies in [l,r][*l*,*r*]. Add P*P* to sum*sum* and set r*r* to point to the parent of P*P*

```java

public int sumRange(int l, int r) {
    // get leaf with value 'l'
    l += n;
    // get leaf with value 'r'
    r += n;
    int sum = 0;
    while (l <= r) {
        if ((l % 2) == 1) {
           sum += tree[l];
           l++;
        }
        if ((r % 2) == 0) {
           sum += tree[r];
           r--;
        }
        l /= 2;
        r /= 2;
    }
    return sum;
}
```

**Complexity Analysis**

- Time complexity : O(log⁡n)*O*(log*n*)
    
    Time complexity is O(log⁡n)*O*(log*n*) because on each iteration of the algorithm we move one level up, either to the parent of the current node or to the next sibling of parent to the left or right direction till the two boundaries meet. In the worst-case scenario this happens at the root after log⁡nlog*n* iterations of the algorithm.
    
- Space complexity : O(1)*O*(1).

```java

```