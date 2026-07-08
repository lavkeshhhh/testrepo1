# Coding Questions — Prep Pack

Codility / online-judge classics. The interviewer tests whether you know the *clever trick*, not brute force. Each question has the optimal solution in **Java** + **Python** and the **key insight to say out loud**.

---

# Q1 — PermMissingElem (Missing Element)

An array `A` consisting of **N** distinct integers is given. The array contains integers in the range `[1..(N + 1)]`, which means that exactly **one** element is missing.

Your goal is to find that missing element.

```java
class Solution { public int solution(int[] A); }
```

that, given an array `A`, returns the value of the missing element.

**Example:**
```
A[0] = 2
A[1] = 3
A[2] = 1
A[3] = 5
```
the function should return **4**, as it is the missing element.

**Assumptions:**
- N is an integer within the range `[0..100,000]`;
- the elements of A are all distinct;
- each element of A is an integer within the range `[1..(N + 1)]`.

### Trick: SUM FORMULA
Sum of `1..(N+1)` is `(N+1)(N+2)/2`. Subtract the actual array sum → the missing number.

```java
class Solution {
    public int solution(int[] A) {
        int n = A.length;
        long expected = (long)(n + 1) * (n + 2) / 2;
        long actual = 0;
        for (int x : A) actual += x;
        return (int)(expected - actual);
    }
}
```

```python
def solution(A):
    n = len(A)
    expected = (n + 1) * (n + 2) // 2
    return expected - sum(A)
```

**Say:** "Sum of 1 to N+1 is `(N+1)(N+2)/2`. The missing element is expected sum minus actual sum. O(N) time, O(1) space. I use `long` in Java to avoid integer overflow."

⚠️ **Overflow trap** — they WANT to see you use `long` in Java. Mention it.

---

# Q2 — OddOccurrencesInArray (Unpaired Element)

A non-empty array `A` consisting of **N** integers is given. The array contains an **odd** number of elements, and each element of the array can be paired with another element that has the same value, **except for one** element that is left unpaired.

```java
class Solution { public int solution(int[] A); }
```

that, given an array `A`, returns the value of the unpaired element.

**Example:**
```
A[0] = 9    A[3] = 9    A[6] = 9
A[1] = 3    A[4] = 9
A[2] = 9    A[5] = 7
```
the function should return **7**, as it is the only element that occurs an odd number of times.

**Assumptions:**
- N is an odd integer within the range `[1..1,000,000]`;
- each element of array A is an integer within the range `[1..1,000,000,000]`;
- all but one of the values in A occur an even number of times.

### Trick: XOR
`x ^ x = 0` and `x ^ 0 = x`. XOR the whole array → all pairs cancel, leaving the unpaired value.

```java
class Solution {
    public int solution(int[] A) {
        int result = 0;
        for (int x : A) result ^= x;
        return result;
    }
}
```

```python
def solution(A):
    result = 0
    for x in A:
        result ^= x
    return result
```

**Say:** "I use XOR. A number XOR'd with itself is zero, and XOR with zero is itself. So XOR-ing the whole array cancels every pair, leaving the unpaired value. O(N) time, **O(1) space** — no hash map needed."

⚠️ Naive answer is a HashMap count (O(N) space). XOR is O(1) space — that's why it impresses.

---

# Q3 — Array Product (Product of Array Except Self)

Given an integer **N** and an array `arr` of **N** integers, return a new array `rarr` of length **N** such that each element `rarr[i]` equals the **product of all elements of `arr` except `arr[i]`**. Solve it **without using division**.

```java
class Solution { public int[] arrayProducts(int n, int[] arr); }
```

**Example:** `n = 4`, `arr = [1, 2, 3, 4]` → returns `[24, 12, 8, 6]`.

**Assumptions:**
- N is an integer within the range `[1..100,000]`;
- each element of arr is an integer;
- the product of any prefix or suffix fits within the integer range;
- you may **not** use division.

### Trick: PREFIX × SUFFIX products
Each answer = (product of everything LEFT) × (product of everything RIGHT). Two passes, no division.

```java
class Solution {
    public int[] arrayProducts(int n, int[] arr) {
        int[] rarr = new int[n];
        rarr[0] = 1;
        for (int i = 1; i < n; i++) {
            rarr[i] = rarr[i - 1] * arr[i - 1];   // left products
        }
        int right = 1;
        for (int i = n - 1; i >= 0; i--) {
            rarr[i] = rarr[i] * right;             // multiply by right products
            right = right * arr[i];
        }
        return rarr;
    }
}
```

```python
def arrayProducts(n, arr):
    rarr = [1] * n
    for i in range(1, n):
        rarr[i] = rarr[i - 1] * arr[i - 1]
    right = 1
    for i in range(n - 1, -1, -1):
        rarr[i] *= right
        right *= arr[i]
    return rarr
```

Walk-through `[1,2,3,4]`:
```
After Pass 1 (left products):  [1, 1, 2, 6]
Pass 2 (running right product):
i=3: rarr[3]=6×1=6,   right=4
i=2: rarr[2]=2×4=8,   right=12
i=1: rarr[1]=1×12=12, right=24
i=0: rarr[0]=1×24=24
Result: [24, 12, 8, 6] ✓
```

**Say:** "Each element's answer is the product of everything to its left times everything to its right. Two passes, O(N) time, no division."

⚠️ **"Why not division?"** trap → "Division fails if any element is 0 — you'd divide by zero. The prefix-suffix approach is division-free and handles zeros."

---

# Q4 — Kth Largest Element

Given an array of integers, find an integer representing the **Kth largest element** in the array.

**Input Format:**
- First line: integer **N**, the size of the array.
- Second line: integer **K**, the Kth position to find.
- Third line: N space-separated integers, the elements of the array.

**Output Format:** A single integer representing the Kth largest element.

**Example:**
```
Input:
5
2
3 1 5 6 4

Output:
5
```
**Explanation:** The second largest element is 5.

**Constraints:**
- `1 ≤ N ≤ 10^5`
- `-10^9 ≤ a[i] ≤ 10^9`
- `1 ≤ K ≤ N`

### Trick: MIN-HEAP of size K (or QuickSelect)
Keep a min-heap of size K. The heap's root is always the Kth largest. O(N log K). The senior answer is **QuickSelect** — O(N) average.

```java
import java.util.PriorityQueue;

class Solution {
    public int findKthLargest(int n, int k, int[] arr) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int x : arr) {
            minHeap.offer(x);
            if (minHeap.size() > k) minHeap.poll();  // drop smallest
        }
        return minHeap.peek();   // root = Kth largest
    }
}
```

```python
import heapq

def findKthLargest(n, k, arr):
    return heapq.nlargest(k, arr)[-1]
    # or: min-heap of size k
    # heap = []
    # for x in arr:
    #     heapq.heappush(heap, x)
    #     if len(heap) > k: heapq.heappop(heap)
    # return heap[0]
```

**Say:** "I keep a min-heap of size K. After processing all elements, the root is the Kth largest. O(N log K) time, O(K) space. The optimal is QuickSelect — partition like quicksort but only recurse into one side — O(N) average time. Sorting the whole array is O(N log N), which is wasteful for a single K."

⚠️ **Trap:** Don't just sort and index — mention heap or QuickSelect to show you know the optimal. Note constraint allows **negative** values, so don't assume positives.

---

# Q5 — Counting Unsafe Passwords

A password system generates passwords made up entirely of digits. A password is **unsafe** if its **first digit** equals its **last digit**.

Given two integers **L** and **R** (inclusive range of possible passwords), count how many passwords in `[L, R]` are unsafe.

**Function:** `solve(L, R)` returns the count of unsafe passwords.

**Input Format (custom testing):** First line contains two space-separated integers **L** and **R**.

**Output Format:** A single integer — total unsafe passwords in `[L, R]`.

**Constraints:**
- `1 ≤ L ≤ R ≤ 10^18`

### Trick: MATH, not iteration (R can be 10^18 — looping TLEs)
Define `f(x)` = count of unsafe numbers in `[1, x]`. Answer = `f(R) - f(L-1)`.

For a number, "first digit == last digit". Single-digit numbers (1–9) are all unsafe (first == last trivially). For multi-digit numbers, count those whose leading digit equals trailing digit using digit math / counting by length.

```java
class Solution {
    // count of unsafe numbers in [1, x]
    static long f(long x) {
        if (x <= 0) return 0;
        long count = 0;
        // single-digit: 1..min(x,9) are all unsafe
        count += Math.min(x, 9);
        // multi-digit numbers, grouped by length d (>=2)
        for (long pow = 10; pow <= x; pow *= 10) {
            // numbers with this many digits: [pow, pow*10 - 1], capped at x
            long lo = pow, hi = Math.min(x, pow * 10 - 1);
            if (lo > hi) break;
            // for a d-digit number, middle digits free (10^(d-2) choices),
            // first digit f in 1..9, last digit must == f.
            long middle = pow / 10;          // 10^(d-2)
            for (long first = 1; first <= 9; first++) {
                // smallest/largest d-digit number starting with `first`, last == first
                long base = first * pow;     // first followed by zeros... approximate
                // count d-digit numbers in [lo,hi] with lead==trail==first
                long start = first * pow + first;            // first,0...0,first (d>=2)
                // iterate middle block range
                long blockLo = first * pow;
                long blockHi = first * pow + pow - 1;
                long a = Math.max(blockLo, lo), b = Math.min(blockHi, hi);
                if (a > b) continue;
                // within this block, last digit cycles 0..9; we want == first.
                // count integers in [a,b] with (n % 10) == first
                count += countWithLastDigit(a, b, first);
            }
        }
        return count;
    }

    // count of integers in [a,b] whose last digit == d
    static long countWithLastDigit(long a, long b, long d) {
        long total = 0;
        // first value >= a with last digit d
        long firstVal = a - (a % 10) + d;
        if (firstVal < a) firstVal += 10;
        if (firstVal > b) return 0;
        return (b - firstVal) / 10 + 1;
    }

    public long solve(long L, long R) {
        return f(R) - f(L - 1);
    }
}
```

```python
def count_with_last_digit(a, b, d):
    first_val = a - (a % 10) + d
    if first_val < a:
        first_val += 10
    if first_val > b:
        return 0
    return (b - first_val) // 10 + 1

def f(x):
    if x <= 0:
        return 0
    count = min(x, 9)            # single-digit 1..9 all unsafe
    pow10 = 10
    while pow10 <= x:
        lo, hi = pow10, min(x, pow10 * 10 - 1)
        for first in range(1, 10):
            block_lo = first * pow10
            block_hi = first * pow10 + pow10 - 1
            a, b = max(block_lo, lo), min(block_hi, hi)
            if a > b:
                continue
            count += count_with_last_digit(a, b, first)
        pow10 *= 10
    return count

def solve(L, R):
    return f(R) - f(L - 1)
```

**Say:** "R is up to 10^18, so iterating is impossible — it has to be a counting/math approach. I use `f(x) = unsafe count in [1, x]`, then answer is `f(R) - f(L-1)` (prefix-count differencing). For each digit-length, the first digit is fixed 1–9, the last must equal it, and the middle digits are free — so I count by formula instead of looping over every number. O(log R) per call. Always use `long` / 64-bit since values reach 10^18."

⚠️ **The trap:** The naive `for i in range(L, R+1)` TLEs instantly at 10^18. The interviewer is checking whether you recognize the **constraint forces a closed-form digit-counting solution** and the **prefix-difference `f(R) - f(L-1)`** pattern.

---

## Quick reference — trick per question

| # | Problem | Trick | Complexity |
|---|---------|-------|-----------|
| 1 | Missing element | Sum formula `(N+1)(N+2)/2`, use `long` | O(N) / O(1) |
| 2 | Unpaired element | XOR cancels pairs | O(N) / O(1) |
| 3 | Product except self | Prefix × suffix, no division | O(N) / O(1) |
| 4 | Kth largest | Min-heap size K / QuickSelect | O(N log K) / O(N) avg |
| 5 | Unsafe passwords | Digit-count math, `f(R)-f(L-1)` | O(log R) |
