# 演算法筆記

## 演算法分類總覽

* Array / String（陣列 / 字串）
* Two Pointers（雙指標）
* Sliding Window（滑動視窗）
* Prefix Sum（前綴和）
* Hash Table / Set（雜湊表 / 集合）
* Stack（堆疊）
* Queue（佇列）
* Linked List（鏈結串列）
* Binary Tree - DFS（二元樹 - 深度優先搜尋）
* Binary Tree - BFS（二元樹 - 廣度優先搜尋）
* Binary Search Tree（二元搜尋樹）
* Graphs - DFS（圖形 - 深度優先搜尋）
* Graphs - BFS（圖形 - 廣度優先搜尋）
* Heap / Priority Queue（堆積 / 優先佇列）
* Binary Search（二元搜尋）
* Backtracking（回溯法）
* DP - 1D（一維動態規劃）
* DP - Multidimensional（多維動態規劃）
* Bit Manipulation（位元運算）
* Trie（字典樹）
* Monotonic Stack（單調堆疊）
* Intervals（區間）

---

## 算法模板

### 1. Array / String（陣列與字串）

通常涉及遍歷、計數或原地修改。核心是利用雜湊表把「查找」從 $O(n)$ 降為 $O(1)$。

```python
def solve(arr):
    count = {}
    for x in arr:
        count[x] = count.get(x, 0) + 1
    return count
```

✅ 用途：

* 字元/元素頻率統計
* 找出重複元素或出現次數最多的元素
* 原地修改陣列（In-place）

---

### 2. Two Pointers（雙指標）

用兩個指標在陣列（或鏈結串列）上移動，避免巢狀迴圈，將 $O(n^2)$ 降為 $O(n)$。

#### 1️⃣ 左右指針（對撞指針）

適用於**排序陣列**或需要從兩端收縮的情況。

```python
def two_pointer_left_right(arr, target):
    left, right = 0, len(arr) - 1

    while left < right:
        current_sum = arr[left] + arr[right]

        if current_sum == target:
            return (left, right)
        elif current_sum < target:
            left += 1   # 左指針右移增大 sum
        else:
            right -= 1  # 右指針左移減小 sum

    return None
```

✅ 用途：

* 有序陣列中找和為 target 的兩數（Two Sum II）
* 最大/最小和問題
* 驗證回文字串

#### 2️⃣ 快慢指針（同向指針）

`slow` 跟踪有效位置，`fast` 掃描整個陣列，用於刪除、重排或鏈結串列。

```python
def two_pointer_fast_slow(arr):
    slow = 0

    for fast in range(len(arr)):
        if arr[fast] != 0:     # 條件：保留非零元素
            arr[slow] = arr[fast]
            slow += 1

    return arr[:slow]
```

✅ 用途：

* 刪除陣列中指定元素（原地）
* 移動零到末尾
* 鏈結串列：偵測環、找中間節點、刪除倒數第 k 個節點

#### 3️⃣ 通用模式總結

* **左右指針**：`left = 0`, `right = len(arr) - 1`，根據條件收縮邊界。
* **快慢指針**：`slow` 跟踪有效位置，`fast` 遍歷整個陣列或鏈表。
* 判斷條件 → 移動指針 → 更新結果。

---

### 3. Sliding Window（滑動視窗）

處理**連續子陣列或子字串**問題，可將 $O(n^2)$ 的暴力解優化到 $O(n)$。

主要分為兩種：**固定長度**與**可變長度**。

#### 1. 固定長度滑動視窗 (Fixed Window)

題目給定視窗大小 `k`，先建立第一個視窗，再向右滑動（進一個、出一個）。

```python
def fixed_sliding_window(nums, k):
    window_state = sum(nums[:k])
    max_res = window_state

    for i in range(k, len(nums)):
        window_state += nums[i] - nums[i - k]  # 加入新元素、移除舊元素
        max_res = max(max_res, window_state)

    return max_res
```

✅ 用途：

* 長度為 k 的子陣列最大平均值
* 固定大小窗口內的最大/最小和

#### 2. 可變長度滑動視窗 (Flexible Window)

右指針 `right` 不斷擴張，當條件不滿足時左指針 `left` 向右收縮。

```python
def flexible_sliding_window(nums):
    left = 0
    res = 0
    window_state = {}

    for right in range(len(nums)):
        # 1. 將右指針元素加入視窗，更新狀態
        window_state[nums[right]] = window_state.get(nums[right], 0) + 1

        # 2. 當視窗不符合條件時，收縮左指針
        while left <= right and condition_not_met():
            window_state[nums[left]] -= 1
            if window_state[nums[left]] == 0:
                del window_state[nums[left]]
            left += 1

        # 3. 視窗合法，更新答案
        res = max(res, right - left + 1)

    return res
```

✅ 用途：

* 最長不重複子字串
* 最小覆蓋子字串（Minimum Window Substring）
* 含最多 k 個不同字元的最長子字串

#### 3. 刷題常用技巧

**何時用滑動視窗？**

* 題目涉及「子陣列」或「子字串」
* 要求「最長/最短/符合條件」的連續區間
* 資料量 $10^5$ 以上，暗示需要 $O(n)$

**狀態維護工具**

* 總和/個數：直接用變數 `curr_sum` 或 `count`
* 字元頻率：`collections.Counter` 或 `dict`
* 唯一性檢查：`set`

**邊界處理**

* 空輸入：`if not s: return 0`
* 視窗長度：`right - left + 1`

---

### 4. Prefix Sum（前綴和）

預處理陣列，使任意區間 $[i, j]$ 的和可以 $O(1)$ 查詢，避免重複計算。

```python
def prefix_sum(nums):
    n = len(nums)
    p_sum = [0] * (n + 1)
    for i in range(n):
        p_sum[i + 1] = p_sum[i] + nums[i]

    # 區間 [i, j] 的和 = p_sum[j+1] - p_sum[i]
    def query(i, j):
        return p_sum[j + 1] - p_sum[i]

    return query
```

✅ 用途：

* 子陣列區間和查詢
* 統計前綴和等於 k 的子陣列數量（搭配 Hash Map）
* 二維前綴和（矩陣區域查詢）

---

### 5. Hash Table / Set（雜湊表 / 集合）

利用 $O(1)$ 的查找速度進行去重或記錄狀態。

```python
def use_hash(nums, target):
    seen = {}
    for i, x in enumerate(nums):
        complement = target - x
        if complement in seen:
            return (seen[complement], i)
        seen[x] = i
    return None
```

✅ 用途：

* Two Sum（查找補數）
* 去重、計頻率
* 記錄已訪問狀態（如 BFS/DFS 的 visited）

---

### 6. Stack（堆疊）

後進先出（LIFO）的線性結構，適合處理「對稱匹配」或「最近一次未處理的元素」。

```python
def is_valid_parentheses(s):
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}

    for char in s:
        if char in mapping:
            top = stack.pop() if stack else '#'
            if mapping[char] != top:
                return False
        else:
            stack.append(char)

    return not stack
```

✅ 用途：

* 括號匹配驗證
* 計算逆波蘭運算式（RPN）
* 函式呼叫追蹤（遞迴改迭代）

---

### 7. Queue / BFS（佇列 / 廣度優先搜尋）

先進先出（FIFO）的線性結構，BFS 的核心工具，保證**按層次**或**最短步數**展開。

```python
from collections import deque

def bfs(root):
    if not root:
        return []
    queue = deque([root])
    result = []

    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)

    return result
```

✅ 用途：

* 二元樹層序遍歷
* 無權圖最短路徑
* 多源 BFS（如腐爛橘子問題）

---

### 8. Linked List（鏈結串列）

鏈結串列的兩大核心模板：**快慢指標** 與 **反轉鏈表**。掌握這兩個模板即可解決絕大多數鏈結串列問題。

#### 模板一：Fast / Slow Pointers（快慢指標）

`slow` 每次走一步，`fast` 每次走兩步。當 `fast` 到終點時，`slow` 恰好在中點。若鏈表有環，快慢指標必定在環內相遇。

```python
# 1. 找中間節點（偶數長度時回傳前半段最後一個）
def find_middle(head):
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # slow 即為中點

# 2. 偵測環（Floyd's Cycle Detection）
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True   # 有環
    return False          # 無環

# 3. 找環的入口節點
def detect_cycle_entry(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            # 相遇後，一個從 head 出發，一個從相遇點出發，再次相遇即為入口
            slow = head
            while slow != fast:
                slow = slow.next
                fast = fast.next
            return slow
    return None
```

✅ 用途：

* 找鏈表中間節點（合併排序鏈表的前置步驟）
* 偵測環（Floyd's Cycle Detection）
* 找環的入口節點（LeetCode 142）
* 判斷鏈表是否為回文（找中點 → 反轉後半段 → 比較）
* 刪除倒數第 N 個節點（fast 先走 N 步，再同步走）

#### 模板二：Reverse Linked List（反轉鏈表）

三指標法：`prev`、`curr`、`next_node`，逐一將箭頭反轉。

```python
# 1. 反轉整條鏈表
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next   # 暫存下一個
        curr.next = prev        # 反轉指向
        prev = curr             # prev 前進
        curr = next_node        # curr 前進
    return prev                 # prev 是新的頭節點

# 2. 反轉區間 [left, right]（LeetCode 92）
def reverse_between(head, left, right):
    dummy = ListNode(0, head)
    prev = dummy

    # 移動到 left 的前一個節點
    for _ in range(left - 1):
        prev = prev.next

    curr = prev.next
    # 反轉 right - left 次
    for _ in range(right - left):
        next_node = curr.next
        curr.next = next_node.next
        next_node.next = prev.next
        prev.next = next_node

    return dummy.next

# 3. K 個一組反轉（LeetCode 25）
def reverse_k_group(head, k):
    dummy = ListNode(0, head)
    group_prev = dummy

    while True:
        # 檢查剩餘節點是否 >= k
        kth = group_prev
        for _ in range(k):
            kth = kth.next
            if not kth:
                return dummy.next

        # 反轉 k 個節點
        prev, curr = kth.next, group_prev.next
        for _ in range(k):
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node

        # 連接反轉後的子鏈表
        tmp = group_prev.next
        group_prev.next = prev
        group_prev = tmp

    return dummy.next
```

✅ 用途：

* 反轉整條鏈結串列（LeetCode 206）
* 反轉指定區間（LeetCode 92）
* K 個一組反轉（LeetCode 25）
* 判斷回文鏈表（搭配快慢指標）
* 兩數相加（反轉後逐位相加）

#### 組合技：快慢指標 + 反轉

許多進階題會同時用到兩個模板：

```python
# 判斷回文鏈表（LeetCode 234）
def is_palindrome(head):
    # Step 1: 快慢指標找中點
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next

    # Step 2: 反轉後半段
    second_half = reverse_list(slow.next)

    # Step 3: 逐一比較
    p1, p2 = head, second_half
    while p2:
        if p1.val != p2.val:
            return False
        p1 = p1.next
        p2 = p2.next
    return True
```

---

### 9. Binary Tree - DFS（二元樹深度優先搜尋）

遞迴遍歷樹結構，分為前序、中序、後序三種。

```python
def dfs(node):
    if not node:
        return 0
    left = dfs(node.left)
    right = dfs(node.right)
    # 後序處理（Post-order）：先得到子樹結果再處理當前節點
    return max(left, right) + 1
```

✅ 用途：

* 計算樹的高度、深度
* 路徑總和（Path Sum）
* 最低共同祖先（LCA）
* 序列化與反序列化

---

### 10. Binary Tree - BFS（二元樹廣度優先搜尋）

使用佇列按層次展開，詳見第 7 節 Queue 模板。

✅ 用途：

* 層序遍歷（Level Order Traversal）
* 找最短路徑層數
* 右視圖（每層最右節點）
* 鋸齒形層序遍歷

---

### 11. Binary Search Tree（二元搜尋樹）

BST 性質：左子樹所有節點 < 根 < 右子樹所有節點。在平衡狀態下搜尋/插入為 $O(\log n)$。

```python
def search_bst(root, val):
    if not root or root.val == val:
        return root
    if val < root.val:
        return search_bst(root.left, val)
    else:
        return search_bst(root.right, val)

def insert_bst(root, val):
    if not root:
        return TreeNode(val)
    if val < root.val:
        root.left = insert_bst(root.left, val)
    else:
        root.right = insert_bst(root.right, val)
    return root
```

✅ 用途：

* 驗證是否為合法 BST
* 中序遍歷得到有序序列
* 找第 k 小的元素

---

### 12. Graphs - DFS（圖形深度優先搜尋）

處理節點連通性，需記錄 `visited` 避免重複訪問（有環圖）。

```python
def graph_dfs(graph, node, visited):
    if node in visited:
        return
    visited.add(node)
    for neighbor in graph[node]:
        graph_dfs(graph, neighbor, visited)
```

✅ 用途：

* 判斷圖的連通分量數量
* 偵測有向圖中的環
* 拓撲排序（Topological Sort）
* 島嶼數量（Island Count）

---

### 13. Graphs - BFS（圖形廣度優先搜尋）

從起點按層次展開，保證找到**最短路徑**（無權圖）。

```python
from collections import deque

def graph_bfs(graph, start):
    visited = {start}
    queue = deque([start])
    distance = {start: 0}

    while queue:
        node = queue.popleft()
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                distance[neighbor] = distance[node] + 1
                queue.append(neighbor)

    return distance
```

✅ 用途：

* 無權圖最短路徑
* 01 矩陣（距離最近的 0）
* 單詞接龍（Word Ladder）

---

### 14. Heap / Priority Queue（堆積 / 優先佇列）

Python 使用 `heapq`（預設最小堆）。需要最大堆時，將值取負號。

```python
import heapq

def top_k_elements(nums, k):
    # 最小堆，維持 k 個最大元素
    heap = []
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap  # heap[0] 是第 k 大的元素

# 最大堆：push -num，pop 後取負號還原
```

✅ 用途：

* Top K 個最大/最小元素
* 合併 K 個有序鏈結串列
* 排程問題（Task Scheduler）
* Dijkstra 最短路徑

---

### 15. Binary Search（二元搜尋）

在**有序**區間中以 $O(\log n)$ 尋找目標值。注意邊界條件（`<=` vs `<`，`mid+1` vs `mid`）。

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

# 找最左邊界（第一個 >= target 的位置）
def lower_bound(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid
    return left
```

✅ 用途：

* 搜尋旋轉排序陣列
* 答案在某個範圍內時「二分答案」
* 找峰值、找插入位置

---

### 16. Backtracking（回溯法）

系統性列舉所有解，透過「選擇 → 遞迴 → 撤銷」的框架剪枝。

```python
def backtrack(path, choices, res):
    # 終止條件：找到一組完整解
    if is_solution(path):
        res.append(path[:])
        return

    for choice in choices:
        # 剪枝：提前排除不合法的選擇
        if not is_valid(choice, path):
            continue

        path.append(choice)               # 做選擇
        backtrack(path, new_choices, res) # 進入下一層
        path.pop()                        # 復原狀態（撤銷選擇）
```

✅ 用途：

* 排列（Permutations）/ 組合（Combinations）/ 子集（Subsets）
* N 皇后問題
* 數獨求解
* 單詞搜尋（Word Search）

---

### 17. DP - 1D（一維動態規劃）

將大問題分解為子問題，利用 `dp` 陣列儲存中間結果避免重複計算。

```python
def dp_1d(n):
    dp = [0] * (n + 1)
    dp[0], dp[1] = 0, 1   # 定義基底案例（base case）

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]  # 狀態轉移方程

    return dp[n]

# 空間優化：若 dp[i] 只依賴前兩項，可壓縮至 O(1)
def dp_1d_optimized(n):
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b
```

✅ 用途：

* 爬樓梯、費波那契
* 打家劫舍（House Robber）
* 最長遞增子序列（LIS）

---

### 18. DP - Multidimensional（多維動態規劃）

處理需要兩個以上維度狀態的問題，常見為二維 $dp[i][j]$。

```python
def dp_2d(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1   # 字符匹配
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])  # 取最大

    return dp[m][n]
```

✅ 用途：

* 最長公共子序列（LCS）
* 編輯距離（Edit Distance）
* 0/1 背包問題
* 唯一路徑（Unique Paths）

---

### 19. Bit Manipulation（位元運算）

直接操作整數的二進位表示，運算速度極快（$O(1)$）。

```python
def bit_tricks(n):
    # 檢查第 i 位是否為 1
    is_set = (n >> i) & 1

    # 設置第 i 位為 1
    n = n | (1 << i)

    # 清除最後一個 1（消去最低位的 1）
    n = n & (n - 1)

    # XOR 消除配對：a ^ a == 0, a ^ 0 == a
    unique = 0
    for x in nums:
        unique ^= x
    return unique
```

✅ 用途：

* 找唯一出現一次的數字（Single Number）
* 計算二進位中 1 的個數（Hamming Weight）
* 判斷是否為 2 的冪次（`n & (n-1) == 0`）
* 子集枚舉（用 bitmask 表示選取狀態）

---

### 20. Trie（字典樹 / 前綴樹）

多叉樹結構，專門用於字串集合的快速插入與前綴搜尋，時間複雜度與字串長度 $L$ 成正比。

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end

    def startsWith(self, prefix: str) -> bool:
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

✅ 用途：

* 自動補全（Auto-complete）
* 搜尋前綴（starts with）
* 單詞搜尋 II（Word Search II）
* 最大異或值（Maximum XOR）

---

### 21. Monotonic Stack（單調堆疊）

維持堆疊內元素嚴格遞增或遞減，專門處理「找下一個更大/更小元素」問題。每個元素**進出堆疊各一次**，時間複雜度 $O(n)$。

```python
def monotonic_stack(nums):
    stack = []   # 存索引（index），比存數值更靈活
    res = [-1] * len(nums)

    for i in range(len(nums)):
        # 單調遞增堆疊（由底到頂遞增）：尋找右側第一個比自己大的元素
        while stack and nums[i] > nums[stack[-1]]:
            index = stack.pop()
            res[index] = nums[i]  # nums[i] 是 index 位置的「下一個更大值」

        stack.append(i)

    return res
```

✅ 用途：

* 下一個更大元素（Next Greater Element）
* 每日溫度（Daily Temperatures）
* 柱狀圖中最大矩形（Largest Rectangle in Histogram）
* 接雨水（Trapping Rain Water）

---

### 22. Intervals（區間問題）

處理區間重疊、合併或插入。**關鍵步驟：先按起點排序**。

```python
def merge_intervals(intervals):
    if not intervals:
        return []

    # 步驟 1：按區間起點排序
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]

    for curr_start, curr_end in intervals[1:]:
        last_start, last_end = merged[-1]

        # 步驟 2：有重疊 → 合併（更新終點）
        if curr_start <= last_end:
            merged[-1][1] = max(last_end, curr_end)
        else:
            # 無重疊 → 直接加入
            merged.append([curr_start, curr_end])

    return merged
```

✅ 用途：

* 合併區間（Merge Intervals）
* 插入區間（Insert Interval）
* 會議室排程（Meeting Rooms）
* 最少箭射爆氣球

---

## 📊 複雜度速查表

### 時間複雜度排序

| 複雜度 | 資料結構 / 演算法 |
| --- | --- |
| **$O(1)$** | Bit Manipulation、Hash Table（平均）、Stack/Queue（基本操作） |
| **$O(\log n)$** | Binary Search、Heap（插入/刪除）、BST（平衡）、Union Find（$O(\alpha(n))$） |
| **$O(n)$** | Array/String、Two Pointers、Sliding Window、Prefix Sum、Linked List、Monotonic Stack、Trie（查找長 $L$ 字串）、1D DP、Tree/Graph DFS&BFS |
| **$O(n \log n)$** | 排序（Timsort）、Heap Sort、Intervals（先排序再線性掃） |
| **$O(n \cdot m)$** | 2D DP、矩陣遍歷 |
| **$O(2^n)$ / $O(n!)$** | Backtracking（列舉所有子集或排列） |

---

### 空間複雜度排序

| 複雜度 | 資料結構 / 演算法 |
| --- | --- |
| **$O(1)$** | Two Pointers、Bit Manipulation、Binary Search（迭代版）、Array 原地修改 |
| **$O(\log n)$** | Binary Search（遞迴版，遞迴棧）、Heap Sort（原地） |
| **$O(H)$** | Tree DFS（$H$ 為樹高，最差 $O(n)$） |
| **$O(W)$** | Tree BFS（$W$ 為最寬層，最差 $O(n)$） |
| **$O(n)$** | Hash Table/Set、Stack/Queue、Linked List、Prefix Sum、1D DP、Monotonic Stack、Heap、Trie |
| **$O(n \cdot m)$** | 2D DP（可優化至 $O(\min(n,m))$） |
| **$O(2^n)$ / $O(n!)$** | Backtracking（儲存所有結果） |

---

### 綜合對照表

| 分類項目 | 時間複雜度 | 空間複雜度 | 備註 |
| --- | --- | --- | --- |
| **Two Pointers** | $O(n)$ | **$O(1)$** | 空間最省的技巧 |
| **Binary Search** | $O(\log n)$ | **$O(1)$** | 遞迴版為 $O(\log n)$ |
| **Hash Table** | $O(1)$ 平均 | **$O(n)$** | 用空間換時間的經典 |
| **DFS (Tree/Graph)** | $O(V+E)$ | **$O(H)$** | $H$ 為樹高（遞迴棧空間） |
| **BFS (Tree/Graph)** | $O(V+E)$ | **$O(W)$** | $W$ 為最大寬度（佇列空間） |
| **1D DP** | $O(n)$ | **$O(n)$** | 可透過「滾動陣列」優化至 $O(1)$ |
| **2D DP** | $O(n \cdot m)$ | **$O(n \cdot m)$** | 可優化至 $O(\min(n, m))$ |
| **Trie** | $O(L)$ | **$O(N \cdot \Sigma)$** | $L$=字串長、$\Sigma$=字母表大小 |
| **Heap** | $O(\log n)$ 插入/刪除 | **$O(n)$** | Python `heapq` 為最小堆 |
| **Union Find** | $O(\alpha(n)) \approx O(1)$ | **$O(n)$** | 需 parent/rank 陣列 |

---

## 🏗️ 排序與內建操作

### 排序

| 操作 | 時間複雜度 | 空間複雜度 | 備註 |
| --- | --- | --- | --- |
| **Python `.sort()` (Timsort)** | $O(n \log n)$ | $O(n)$ | 穩定排序 |
| **Built-in Binary Search** | $O(\log n)$ | $O(1)$ | Python `bisect` |
| **Quick Select（第 K 大）** | $O(n)$ 平均 | $O(1)$ | 最差 $O(n^2)$ |

### 常用容器操作

| 容器 | 操作 | 時間複雜度 |
| --- | --- | --- |
| **List** | 隨機存取 `arr[i]` | $O(1)$ |
| **List** | 末端插入/刪除 `append`/`pop` | $O(1)$ 均攤 |
| **List** | 中間插入/刪除 `insert`/`remove` | **$O(n)$** |
| **List** | 元素搜尋 `x in arr` | **$O(n)$** |
| **Deque** | 兩端插入/刪除 | $O(1)$ |
| **Dict/Set** | 查找/插入/刪除 | $O(1)$ 平均 |

---

## 🧩 進階分類補充

### Union Find（聯集查找 / 並查集）

處理圖論連通性，`find` 加路徑壓縮、`union` 加 rank 優化後接近 $O(1)$。

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # 路徑壓縮
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True
```

✅ 用途：

* 島嶼數量（Union Find 版）
* 冗餘連接（Redundant Connection）
* 判斷無向圖是否有環

### 字串處理注意事項

* **字串拼接**：Python 字串不可變，`s + t` 為 $O(n + m)$。建議用 `list` 收集後 `''.join(list)`。
* **子字串切片 `s[i:j]`**：$O(k)$，$k$ 為子字串長度，非 $O(1)$。

### 面試空間複雜度說明

面試官通常問的是**額外空間（Auxiliary Space）**，即除了輸入/輸出以外所使用的空間。如果回傳一個答案陣列，通常不計入空間複雜度，除非題目特別要求。

---

## 💡 最佳化技巧提示

1. **DP 空間壓縮**：若 $dp[i]$ 只依賴 $dp[i-1]$，可壓縮至 $O(1)$（使用兩個變數滾動）。
2. **原地修改（In-place）**：確認題目允許後，直接修改輸入陣列，避免額外空間。
3. **排序前置**：區間、貪心類問題通常先排序，再線性掃描。
4. **Bitmask 枚舉子集**：$n$ 個元素的子集可用 `for mask in range(1 << n)` 枚舉。
