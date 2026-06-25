

## 1. Reverse Linked List

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        prev, curr = None, head
        while curr:
            nxt = curr.next
            curr.next = prev
            prev = curr
            curr = nxt
        return prev
```

---

## 2. Linked List Cycle II

```python
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        slow = fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                slow2 = head
                while slow != slow2:
                    slow = slow.next
                    slow2 = slow2.next
                return slow
        return None
```

---

## 3. Merge Two Sorted Lists

```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        dummy = ListNode(0)
        curr = dummy
        while l1 and l2:
            if l1.val <= l2.val:
                curr.next = l1
                l1 = l1.next
            else:
                curr.next = l2
                l2 = l2.next
            curr = curr.next
        curr.next = l1 or l2
        return dummy.next
```

---

## 4. Remove Nth Node from End of List

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(0, head)
        fast = slow = dummy
        for _ in range(n + 1):
            fast = fast.next
        while fast:
            fast = fast.next
            slow = slow.next
        slow.next = slow.next.next
        return dummy.next
```

---

## 5. Reorder List

```python
class Solution:
    def reorderList(self, head: ListNode) -> None:
        # Step 1: Find middle
        slow, fast = head, head.next
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next

        # Step 2: Reverse second half
        second = slow.next
        slow.next = None
        prev = None
        while second:
            nxt = second.next
            second.next = prev
            prev = second
            second = nxt
        second = prev

        # Step 3: Merge two halves
        first = head
        while second:
            tmp1, tmp2 = first.next, second.next
            first.next = second
            second.next = tmp1
            first = tmp1
            second = tmp2
```

---

## 6. Copy List with Random Pointer

```python
class Node:
    def __init__(self, x: int, next=None, random=None):
        self.val = x
        self.next = next
        self.random = random

class Solution:
    def copyRandomList(self, head: Node) -> Node:
        old_to_new = {None: None}
        curr = head
        while curr:
            old_to_new[curr] = Node(curr.val)
            curr = curr.next
        curr = head
        while curr:
            old_to_new[curr].next   = old_to_new[curr.next]
            old_to_new[curr].random = old_to_new[curr.random]
            curr = curr.next
        return old_to_new[head]
```

---

## 7. Valid Parentheses

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        mapping = {')': '(', '}': '{', ']': '['}
        for ch in s:
            if ch in mapping:
                if not stack or stack[-1] != mapping[ch]:
                    return False
                stack.pop()
            else:
                stack.append(ch)
        return not stack
```

---

## 8. Min Stack

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        min_val = min(val, self.min_stack[-1] if self.min_stack else val)
        self.min_stack.append(min_val)

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

---

## 9. Daily Temperatures (Monotonic Stack)

```python
from typing import List

class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        result = [0] * len(temperatures)
        stack = []  # stores indices

        for i, temp in enumerate(temperatures):
            while stack and temp > temperatures[stack[-1]]:
                idx = stack.pop()
                result[idx] = i - idx
            stack.append(i)

        return result
```

---

## 10. Largest Rectangle in Histogram

```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        stack = []  # (index, height)
        max_area = 0

        for i, h in enumerate(heights):
            start = i
            while stack and stack[-1][1] > h:
                idx, height = stack.pop()
                max_area = max(max_area, height * (i - idx))
                start = idx
            stack.append((start, h))

        for idx, height in stack:
            max_area = max(max_area, height * (len(heights) - idx))

        return max_area
```

---

## 11. Sliding Window Maximum

```python
from collections import deque

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        dq = deque()  # stores indices, front = max
        result = []

        for i, n in enumerate(nums):
            # Remove elements outside the window
            if dq and dq[0] < i - k + 1:
                dq.popleft()
            # Remove smaller elements from the back
            while dq and nums[dq[-1]] < n:
                dq.pop()
            dq.append(i)
            if i >= k - 1:
                result.append(nums[dq[0]])

        return result
```

---

## 12. Longest Consecutive Sequence

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        num_set = set(nums)
        longest = 0

        for n in num_set:
            if n - 1 not in num_set:   # start of a sequence
                length = 1
                while n + length in num_set:
                    length += 1
                longest = max(longest, length)

        return longest
```

