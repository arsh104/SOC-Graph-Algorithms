

## 1. Binary Search



```python
from typing import List

class Solution:
    def search(self, nums: List[int], target: int) -> int:
        lo, hi = 0, len(nums) - 1

        while lo <= hi:
            mid = (lo + hi) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid - 1

        return -1
```

---

## 2. Search in Rotated Sorted Array


```python
from typing import List

class Solution:
    def search(self, nums: List[int], target: int) -> int:
        lo, hi = 0, len(nums) - 1

        while lo <= hi:
            mid = (lo + hi) // 2

            if nums[mid] == target:
                return mid

            # Left half is sorted
            if nums[lo] <= nums[mid]:
                if nums[lo] <= target < nums[mid]:
                    hi = mid - 1
                else:
                    lo = mid + 1
            # Right half is sorted
            else:
                if nums[mid] < target <= nums[hi]:
                    lo = mid + 1
                else:
                    hi = mid - 1

        return -1
```

---

## 3. Find First and Last Position of Element in Sorted Array



```python
from typing import List

class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        def find_left(nums, target):
            lo, hi, result = 0, len(nums) - 1, -1
            while lo <= hi:
                mid = (lo + hi) // 2
                if nums[mid] == target:
                    result = mid
                    hi = mid - 1        # keep searching left
                elif nums[mid] < target:
                    lo = mid + 1
                else:
                    hi = mid - 1
            return result

        def find_right(nums, target):
            lo, hi, result = 0, len(nums) - 1, -1
            while lo <= hi:
                mid = (lo + hi) // 2
                if nums[mid] == target:
                    result = mid
                    lo = mid + 1        # keep searching right
                elif nums[mid] < target:
                    lo = mid + 1
                else:
                    hi = mid - 1
            return result

        return [find_left(nums, target), find_right(nums, target)]
```

---

## 4. Median of Two Sorted Arrays


```python
from typing import List

class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        # Ensure nums1 is the smaller array
        if len(nums1) > len(nums2):
            nums1, nums2 = nums2, nums1

        m, n = len(nums1), len(nums2)
        half = (m + n) // 2
        lo, hi = 0, m

        while lo <= hi:
            i = (lo + hi) // 2      # partition index for nums1
            j = half - i            # partition index for nums2

            # Edge-case sentinels
            left1  = nums1[i - 1] if i > 0 else float('-inf')
            right1 = nums1[i]     if i < m else float('inf')
            left2  = nums2[j - 1] if j > 0 else float('-inf')
            right2 = nums2[j]     if j < n else float('inf')

            if left1 <= right2 and left2 <= right1:
                # Correct partition found
                if (m + n) % 2 == 1:
                    return float(min(right1, right2))
                return (max(left1, left2) + min(right1, right2)) / 2.0
            elif left1 > right2:
                hi = i - 1
            else:
                lo = i + 1

        return -1.0
```

---

## 5. Koko Eating Bananas



```python
from typing import List
import math

class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def hours_needed(speed):
            return sum(math.ceil(p / speed) for p in piles)

        lo, hi = 1, max(piles)

        while lo < hi:
            mid = (lo + hi) // 2
            if hours_needed(mid) <= h:
                hi = mid            # try slower speed
            else:
                lo = mid + 1        # need faster speed

        return lo
```

---

## 6. Sort Colors (Dutch National Flag)



```python
from typing import List

class Solution:
    def sortColors(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        low, mid, high = 0, 0, len(nums) - 1

        while mid <= high:
            if nums[mid] == 0:
                nums[low], nums[mid] = nums[mid], nums[low]
                low += 1
                mid += 1
            elif nums[mid] == 1:
                mid += 1
            else:  # nums[mid] == 2
                nums[mid], nums[high] = nums[high], nums[mid]
                high -= 1
                # don't increment mid — the swapped element is unexamined
```

---

## 7. Merge Intervals



```python
from typing import List

class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort(key=lambda x: x[0])
        merged = [intervals[0]]

        for start, end in intervals[1:]:
            last_end = merged[-1][1]

            if start <= last_end:
                # Overlapping — extend the last interval if needed
                merged[-1][1] = max(last_end, end)
            else:
                # Non-overlapping — add as new interval
                merged.append([start, end])

        return merged
```

---

## 8. Top K Frequent Elements


```python
from typing import List
from collections import Counter

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = Counter(nums)

        # Bucket index = frequency, max frequency = len(nums)
        buckets = [[] for _ in range(len(nums) + 1)]
        for num, freq in count.items():
            buckets[freq].append(num)

        result = []
        for freq in range(len(buckets) - 1, 0, -1):
            for num in buckets[freq]:
                result.append(num)
                if len(result) == k:
                    return result

        return result
```

---

## 9. Subsets


```python
from typing import List

class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []

        def backtrack(start: int, current: List[int]):
            result.append(current[:])           # every path is a valid subset

            for i in range(start, len(nums)):
                current.append(nums[i])
                backtrack(i + 1, current)
                current.pop()                   # undo choice

        backtrack(0, [])
        return result
```

---

## 10. Permutations


```python
from typing import List

class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []

        def backtrack(start: int):
            if start == len(nums):
                result.append(nums[:])
                return

            for i in range(start, len(nums)):
                nums[start], nums[i] = nums[i], nums[start]   # choose
                backtrack(start + 1)
                nums[start], nums[i] = nums[i], nums[start]   # undo

        backtrack(0)
        return result
```

---

## 11. Combination Sum


```python
from typing import List

class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        result = []
        candidates.sort()

        def backtrack(start: int, current: List[int], remaining: int):
            if remaining == 0:
                result.append(current[:])
                return

            for i in range(start, len(candidates)):
                if candidates[i] > remaining:
                    break               # sorted — no point continuing

                current.append(candidates[i])
                backtrack(i, current, remaining - candidates[i])  # i (not i+1) allows reuse
                current.pop()

        backtrack(0, [], target)
        return result
```

---

## 12. N-Queens


```python
from typing import List

class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        result = []
        cols = set()
        pos_diag = set()    # row + col is constant on '/' diagonals
        neg_diag = set()    # row - col is constant on '\' diagonals
        board = [['.'] * n for _ in range(n)]

        def backtrack(row: int):
            if row == n:
                result.append([''.join(r) for r in board])
                return

            for col in range(n):
                if col in cols or (row + col) in pos_diag or (row - col) in neg_diag:
                    continue

                # Place queen
                cols.add(col)
                pos_diag.add(row + col)
                neg_diag.add(row - col)
                board[row][col] = 'Q'

                backtrack(row + 1)

                # Remove queen (backtrack)
                cols.remove(col)
                pos_diag.remove(row + col)
                neg_diag.remove(row - col)
                board[row][col] = '.'

        backtrack(0)
        return result
```
