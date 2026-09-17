# Linked Lists — Templates

```python
class ListNode:
    def __init__(self, val=0, nxt=None):
        self.val = val
        self.next = nxt
```

## 1. Dummy head
```python
dummy = ListNode(0, head)
prev = dummy
while prev.next:
    if should_remove(prev.next): prev.next = prev.next.next
    else: prev = prev.next
return dummy.next
```

## 2. Reverse — iterative and recursive
```python
def reverse(head):
    prev, cur = None, head
    while cur:
        nxt = cur.next        # save BEFORE overwriting
        cur.next = prev
        prev, cur = cur, nxt
    return prev

def reverse_rec(head):
    if not head or not head.next: return head
    new_head = reverse_rec(head.next)
    head.next.next = head
    head.next = None
    return new_head
```

## 3. Middle of the list
```python
slow = fast = head
while fast and fast.next:           # second middle for even length
    slow, fast = slow.next, fast.next.next
return slow
# first middle for even length: while fast.next and fast.next.next
```

## 4. Cycle detection and entry point
```python
def detect_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
        if slow is fast:
            slow = head
            while slow is not fast:
                slow, fast = slow.next, fast.next
            return slow                 # entry node
    return None
```

## 5. Remove n-th from the end
```python
dummy = ListNode(0, head)
fast = slow = dummy
for _ in range(n): fast = fast.next
while fast.next:
    fast, slow = fast.next, slow.next
slow.next = slow.next.next
return dummy.next
```

## 6. Merge two sorted lists
```python
def merge(a, b):
    dummy = tail = ListNode()
    while a and b:
        if a.val <= b.val: tail.next, a = a, a.next
        else:              tail.next, b = b, b.next
        tail = tail.next
    tail.next = a or b                  # attach the remainder
    return dummy.next
```

## 7. Merge k sorted lists — heap, O(N log k)
```python
import heapq
def merge_k(lists):
    h = [(node.val, i, node) for i, node in enumerate(lists) if node]
    heapq.heapify(h)                    # i breaks ties: ListNode is not comparable
    dummy = tail = ListNode()
    while h:
        _, i, node = heapq.heappop(h)
        tail.next = node; tail = node
        if node.next: heapq.heappush(h, (node.next.val, i, node.next))
    return dummy.next
```

## 8. Palindrome check in O(1) space
```python
def is_palindrome(head):
    slow = fast = head
    while fast and fast.next: slow, fast = slow.next, fast.next.next
    second = reverse(slow)              # reverse the back half
    p, q = head, second
    while q:
        if p.val != q.val: return False
        p, q = p.next, q.next
    return True
```

## 9. Reorder list (L0 → Ln → L1 → Ln-1 → …)
```python
slow = fast = head
while fast.next and fast.next.next: slow, fast = slow.next, fast.next.next
second = reverse(slow.next); slow.next = None
first = head
while second:
    f_nxt, s_nxt = first.next, second.next
    first.next = second; second.next = f_nxt
    first, second = f_nxt, s_nxt
```

## 10. Reverse nodes in k-groups
```python
def reverse_k_group(head, k):
    node, count = head, 0
    while node and count < k: node = node.next; count += 1
    if count < k: return head            # fewer than k left: leave as is
    prev = reverse_k_group(node, k)      # reverse the rest first
    cur = head
    for _ in range(k):
        nxt = cur.next; cur.next = prev; prev = cur; cur = nxt
    return prev
```

## 11. LRU Cache
```python
class Node:
    def __init__(self, k=0, v=0):
        self.k, self.v = k, v
        self.prev = self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.map = {}
        self.head, self.tail = Node(), Node()      # sentinels
        self.head.next, self.tail.prev = self.tail, self.head

    def _unlink(self, n):
        n.prev.next, n.next.prev = n.next, n.prev

    def _push_front(self, n):
        n.next, n.prev = self.head.next, self.head
        self.head.next.prev = n
        self.head.next = n

    def get(self, key):
        if key not in self.map: return -1
        n = self.map[key]
        self._unlink(n); self._push_front(n)
        return n.v

    def put(self, key, value):
        if key in self.map:
            n = self.map[key]; n.v = value
            self._unlink(n); self._push_front(n); return
        if len(self.map) == self.cap:
            lru = self.tail.prev
            self._unlink(lru); del self.map[lru.k]
        n = Node(key, value)
        self.map[key] = n; self._push_front(n)
```

## 12. Copy List with Random Pointer — O(1) extra space
```python
# 1: interleave copies   A -> A' -> B -> B' -> ...
cur = head
while cur:
    cur.next = Node(cur.val, cur.next); cur = cur.next.next
# 2: wire the random pointers
cur = head
while cur:
    if cur.random: cur.next.random = cur.random.next
    cur = cur.next.next
# 3: separate the two lists
```

## C++ notes
- `ListNode* dummy = new ListNode(0); dummy->next = head;` — remember to free, or use a stack-allocated dummy.
- `while (fast && fast->next)` — both checks, in this order, or you dereference null.
- Comparable wrapper needed for `priority_queue<ListNode*>`: supply a comparator on `->val`.
