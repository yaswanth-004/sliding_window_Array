
# 🪟 Sliding Window — Complete DSA Guide

> Master the Sliding Window technique for Arrays and Strings

---

## 📌 What is Sliding Window?

A **Sliding Window** is a technique where you maintain a **window (subarray/substring)** that slides over the data to avoid repeated computation.

Instead of using nested loops **(O(n²))**, we expand or shrink the window from the **right (+)** and **left (-)** ends to get **O(n)** solutions.

```
Array:  [2, 1, 5, 1, 3, 2]
         ↑           ↑
        left        right   → window slides →
```

---

## 🧠 Core Idea

```
RIGHT (+) → Expand the window (add element)
LEFT  (-) → Shrink the window (remove element)
```

- **Fixed Window** → window size `k` is constant
- **Variable Window** → window grows/shrinks based on a condition

---

## ⏱️ Complexity

| Type | Time | Space |
|------|------|-------|
| Fixed Window | O(n) | O(1) |
| Variable Window | O(n) | O(1) or O(k) |
| With HashMap | O(n) | O(k) |

> **Why O(n)?** Each element is added (right+) and removed (left-) at most once.

---

## 🔧 Template — Fixed Window

```python
def fixed_window(arr, k):
    left = 0
    window_sum = 0
    result = 0

    for right in range(len(arr)):
        window_sum += arr[right]          # RIGHT + expand

        if right - left + 1 == k:         # window size reached
            result = max(result, window_sum)
            window_sum -= arr[left]       # LEFT - shrink
            left += 1

    return result
```

---

## 🔧 Template — Variable Window

```python
def variable_window(arr, condition):
    left = 0
    window_val = 0
    result = 0

    for right in range(len(arr)):
        window_val += arr[right]          # RIGHT + expand

        while not valid(window_val):      # condition broken
            window_val -= arr[left]       # LEFT - shrink
            left += 1

        result = max(result, right - left + 1)

    return result
```

---

## 📚 Problem Types & Solutions

---

### 🔷 Type 1 — Maximum Subarray Sum (Fixed Window)

**Problem:** Find max sum of subarray of size `k`

```
Input:  arr = [2, 1, 5, 1, 3, 2], k = 3
Output: 9  (subarray [5, 1, 3])
```

```python
def max_subarray_sum(arr, k):
    left = 0
    window_sum = 0
    max_sum = float('-inf')

    for right in range(len(arr)):
        window_sum += arr[right]          # RIGHT + add

        if right - left + 1 == k:
            max_sum = max(max_sum, window_sum)
            window_sum -= arr[left]       # LEFT - remove
            left += 1

    return max_sum

# Time: O(n)  Space: O(1)
```

---

### 🔷 Type 2 — Longest Subarray with Sum ≤ K (Variable Window)

**Problem:** Find longest subarray where sum ≤ k

```
Input:  arr = [3, 1, 2, 1, 1, 3], k = 5
Output: 4  (subarray [1, 2, 1, 1])
```

```python
def longest_subarray_sum_k(arr, k):
    left = 0
    window_sum = 0
    max_len = 0

    for right in range(len(arr)):
        window_sum += arr[right]          # RIGHT + expand

        while window_sum > k:             # condition broken
            window_sum -= arr[left]       # LEFT - shrink
            left += 1

        max_len = max(max_len, right - left + 1)

    return max_len

# Time: O(n)  Space: O(1)
```

---

### 🔷 Type 3 — Fruits Into Baskets (Variable Window + HashMap)

**Problem:** You have 2 baskets, each can hold 1 fruit type. Find max fruits you can pick.
(= Longest subarray with at most 2 distinct elements)

```
Input:  arr = [1, 2, 1, 2, 3]
Output: 4  ([1, 2, 1, 2])
```

```python
from collections import defaultdict

def fruits_into_baskets(arr):
    left = 0
    basket = defaultdict(int)   # fruit -> count
    max_fruits = 0

    for right in range(len(arr)):
        basket[arr[right]] += 1          # RIGHT + add fruit

        while len(basket) > 2:           # more than 2 types
            basket[arr[left]] -= 1       # LEFT - remove fruit
            if basket[arr[left]] == 0:
                del basket[arr[left]]
            left += 1

        max_fruits = max(max_fruits, right - left + 1)

    return max_fruits

# Time: O(n)  Space: O(k) where k = distinct fruits
```

---

### 🔷 Type 4 — Longest Substring Without Repeating Characters

**Problem:** Find length of longest substring with all unique characters

```
Input:  s = "abcabcbb"
Output: 3  ("abc")
```

```python
def length_of_longest_substring(s):
    left = 0
    char_set = set()
    max_len = 0

    for right in range(len(s)):
        while s[right] in char_set:      # duplicate found
            char_set.remove(s[left])     # LEFT - shrink
            left += 1

        char_set.add(s[right])           # RIGHT + add
        max_len = max(max_len, right - left + 1)

    return max_len

# Time: O(n)  Space: O(k) where k = charset size
```

---

### 🔷 Type 5 — Minimum Window Substring

**Problem:** Find smallest substring of `s` that contains all chars of `t`

```
Input:  s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
```

```python
from collections import Counter

def min_window(s, t):
    need = Counter(t)
    missing = len(t)
    left = 0
    result = ""
    min_len = float('inf')

    for right in range(len(s)):
        if need[s[right]] > 0:
            missing -= 1
        need[s[right]] -= 1              # RIGHT + expand

        while missing == 0:              # valid window
            if right - left + 1 < min_len:
                min_len = right - left + 1
                result = s[left:right+1]
            need[s[left]] += 1
            if need[s[left]] > 0:
                missing += 1
            left += 1                    # LEFT - shrink

    return result

# Time: O(n)  Space: O(k)
```

---

### 🔷 Type 6 — Subarray Product Less Than K

**Problem:** Count subarrays where product of all elements < k

```
Input:  arr = [10, 5, 2, 6], k = 100
Output: 8
```

```python
def num_subarray_product_less_than_k(arr, k):
    if k <= 1:
        return 0

    left = 0
    product = 1
    count = 0

    for right in range(len(arr)):
        product *= arr[right]             # RIGHT + multiply

        while product >= k:
            product //= arr[left]         # LEFT - divide
            left += 1

        count += right - left + 1        # all subarrays ending at right

    return count

# Time: O(n)  Space: O(1)
```

---

### 🔷 Type 7 — Max Consecutive Ones III (Flip at most K zeros)

**Problem:** Flip at most k 0s, find longest subarray of 1s

```
Input:  arr = [1,1,1,0,0,0,1,1,1,1,0], k = 2
Output: 6
```

```python
def longest_ones(arr, k):
    left = 0
    zeros = 0
    max_len = 0

    for right in range(len(arr)):
        if arr[right] == 0:
            zeros += 1                   # RIGHT + count zeros

        while zeros > k:
            if arr[left] == 0:
                zeros -= 1              # LEFT - unccount zero
            left += 1

        max_len = max(max_len, right - left + 1)

    return max_len

# Time: O(n)  Space: O(1)
```

---

### 🔷 Type 8 — Longest Subinterval / Subarray with Equal 0s and 1s

**Problem:** Find longest subarray with equal number of 0s and 1s

```
Input:  arr = [0, 1, 0, 0, 1, 1, 0]
Output: 6
```

```python
def find_max_length(arr):
    # Replace 0 with -1, then find longest subarray with sum = 0
    prefix = {0: -1}
    running_sum = 0
    max_len = 0

    for i, num in enumerate(arr):
        running_sum += 1 if num == 1 else -1

        if running_sum in prefix:
            max_len = max(max_len, i - prefix[running_sum])
        else:
            prefix[running_sum] = i

    return max_len

# Time: O(n)  Space: O(n)
```

---

## 🗺️ Problem → Pattern Map

| Problem | Window Type | Key Data Structure |
|---------|-------------|-------------------|
| Max subarray sum of size k | Fixed | Variable |
| Longest subarray sum ≤ k | Variable | Variable |
| Fruits into baskets | Variable | HashMap |
| Longest unique substring | Variable | HashSet |
| Minimum window substring | Variable | HashMap |
| Product < k count | Variable | Variable |
| Max ones with k flips | Variable | Counter |
| Equal 0s and 1s | Prefix Sum | HashMap |

---

## 💡 When to Use Sliding Window?

✅ Use when:
- Problem asks for **subarray / substring**
- Condition involves **sum, count, distinct elements**
- Brute force is O(n²) or O(n³)

❌ Don't use when:
- Elements are not contiguous
- Order doesn't matter

---

## 🔑 Key Insight

```
right (+) always moves forward → O(n)
left  (-) only moves forward  → O(n)
Total = O(n) + O(n) = O(n)
```

> Never move `left` backwards — that's what keeps it linear!
