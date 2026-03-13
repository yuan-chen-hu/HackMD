# 演算法筆記

Array / String (陣列 / 字串)

Two Pointers (雙指標)

Sliding Window (滑動視窗)

Prefix Sum (前綴和)

Hash Table / Set (雜湊表 / 集合)

Stack (堆疊)

Queue (佇列)

Linked List (鏈結串列)

Binary Tree - DFS (二元樹 - 深度優先搜尋)

Binary Tree - BFS (二元樹 - 廣度優先搜尋)

Binary Search Tree (二元搜尋樹)

Graphs - DFS (圖形 - 深度優先搜尋)

Graphs - BFS (圖形 - 廣度優先搜尋)

Heap / Priority Queue (堆積 / 優先佇列)

Binary Search (二元搜尋)

Backtracking (回溯法)

DP - 1D (一維動態規劃)

DP - Multidimensional (多維動態規劃)

Bit Manipulation (位元運算)

Trie (字典樹)

Monotonic Stack (單調堆疊)

Intervals (區間)


---
## 算法模板
### 1. Array / String (陣列與字串)

通常涉及遍歷、計數或原地修改。

```python
def solve(arr):
    # 常見：遍歷、計數或雜湊映射
    count = {}
    for x in arr:
        count[x] = count.get(x, 0) + 1
    return ...

```

### 2. Two Pointers (雙指標)





#### 1️⃣ 左右指針模板（典型用於排序陣列或需要從兩端收縮的情況）

```python
def two_pointer_left_right(arr, target):
    left, right = 0, len(arr) - 1

    while left < right:
        current_sum = arr[left] + arr[right]

        if current_sum == target:
            # 找到一組解
            return (left, right)
        elif current_sum < target:
            left += 1  # 左指針右移增大 sum
        else:
            right -= 1  # 右指針左移減小 sum

    return None  # 沒有解
```

✅ 用途：

* 有序數組找和為 target 的兩數
* 最大/最小和問題
* “收縮窗口”類型的問題



#### 2️⃣ 快慢指針模板（同方向移動，通常用於刪除/重排/鏈表）

```python
def two_pointer_fast_slow(arr):
    slow = 0

    for fast in range(len(arr)):
        # 判斷條件，例如刪除特定元素
        if arr[fast] != 0:
            arr[slow] = arr[fast]
            slow += 1

    # slow 指向最後有效元素位置
    return arr[:slow]
```

✅ 用途：

* 刪除陣列中指定元素
* 重排元素（如移動零到末尾）
* 鏈表相關問題（檢測循環、刪除節點等）


#### 3️⃣ 通用模式總結

* **左右指針**：`left = 0`, `right = len(arr) - 1`，根據條件收縮邊界。
* **快慢指針**：`slow` 跟踪有效位置，`fast` 遍歷整個陣列或鏈表。
* 判斷條件 → 移動指針 → 更新結果。




### 3. Sliding Window (滑動視窗)

處理子陣列、子字串問題。

```python
def sliding_window(s):
    left = 0
    res = 0
    window = {}
    for right in range(len(s)):
        # 加入右邊元素
        window[s[right]] = window.get(s[right], 0) + 1
        # 當條件不滿足時，收縮左邊邊界
        while condition_not_met:
            window[s[left]] -= 1
            left += 1
        res = max(res, right - left + 1)
    return res

```

滑動視窗（Sliding Window）是解決「子陣列」或「子字串」問題的神器。它可以將 $O(n^2)$ 的暴力解優化到 $O(n)$。

滑動視窗主要分為兩種：**固定長度**與**可變長度**。以下為你整理這兩類的通用 Python 模板。

---

#### 1. 固定長度滑動視窗 (Fixed Window)

這種情況通常題目會給定視窗大小為 `k`。

**核心邏輯：** 先建立第一個視窗，然後向右滑動，每次「加一個進來，減一個出去」。

```python
def fixed_sliding_window(nums, k):
    # 1. 初始化第一個視窗的狀態 (例如總和、計數)
    window_state = sum(nums[:k])
    max_res = window_state
    
    # 2. 開始從 k 滑動到結尾
    for i in range(k, len(nums)):
        # 進入視窗的新元素: nums[i]
        # 離開視窗的舊元素: nums[i - k]
        window_state += nums[i] - nums[i - k]
        
        # 3. 更新全局最大/最小值
        max_res = max(max_res, window_state)
        
    return max_res

```

---

#### 2. 可變長度滑動視窗 (Flexible Window)

這類題目最常見（例如：最長不重複子字串）。視窗的大小會根據條件（`condition`）動態收縮。

**核心邏輯：** 右指針 `right` 不斷向右擴張，當條件不滿足時，左指針 `left` 才向右收縮。

```python
def flexible_sliding_window(nums):
    left = 0
    res = 0
    window_state = {} # 或用 counter, set 等
    
    for right in range(len(nums)):
        # 1. 將右指針元素加入視窗，更新狀態
        # update_state(nums[right])
        
        # 2. 當視窗內的狀態不符合題目要求時，收縮左指針
        while left <= right and condition_not_met():
            # 移除左指針元素，更新狀態
            # remove_state(nums[left])
            left += 1
            
        # 3. 視窗合法，在此處更新答案 (可能是長度或最大值)
        res = max(res, right - left + 1)
        
    return res

```

---

#### 3. 刷題常用技巧與細節

##### 什麼時候用滑動視窗？

* 題目出現「子陣列 (Subarray)」或「子字串 (Substring)」。
* 要求「最長/最短/符合條件」的連續區間。
* 數據量很大（$10^5$ 以上），暗示需要 $O(n)$ 算法。

##### 狀態維護工具

* **總和/個數**：直接用變數 `curr_sum` 或 `count`。
* **字元頻率**：使用 `collections.Counter` 或 `dict`。
* **唯一性檢查**：使用 `set`。

##### 邊界處理

* **空輸入**：一開始先判斷 `if not s: return 0`。
* **視窗大小**：注意 `right - left + 1` 是目前視窗的長度。

---


### 4. Prefix Sum (前綴和)

用於快速計算子陣列區間和。

```python
def prefix_sum(nums):
    p_sum = [0] * (len(nums) + 1)
    for i in range(len(nums)):
        p_sum[i+1] = p_sum[i] + nums[i]
    # 區間 [i, j] 的和 = p_sum[j+1] - p_sum[i]

```

### 5. Hash Table / Set (雜湊表)

利用 $O(1)$ 的查找速度進行去重或記錄狀態。

```python
def use_hash(nums):
    lookup = set(nums) # 或 dict()
    if target in lookup:
        return True

```

### 6. Stack / Monotonic Stack (堆疊 / 單調堆疊)

處理括號匹配或「下一個更大元素」問題。

```python
def monotonic_stack(arr):
    stack = []
    for x in arr:
        while stack and stack[-1] < x: # 或 > x
            stack.pop()
        stack.append(x)

```

### 7. Queue / BFS (佇列 / 廣度優先搜尋)

處理層序遍歷或最短路徑。

```python
from collections import deque
def bfs(root):
    queue = deque([root])
    while queue:
        node = queue.popleft()
        if node.left: queue.append(node.left)
        if node.right: queue.append(node.right)

```

### 8. Linked List (鏈結串列)

常規操作：反轉、快慢指標。

```python
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    return prev

```

### 9. Binary Tree - DFS (深度優先搜尋)

遞迴遍歷樹結構。

```python
def dfs(node):
    if not node: return 0
    left = dfs(node.left)
    right = dfs(node.right)
    return max(left, right) + 1

```

### 10. Binary Search (二元搜尋)

在有序區間中尋找特定值。

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

```

### 11. Backtracking (回溯法)

尋找所有可能的排列、組合。

```python
def backtrack(path, choices):
    if is_solution(path):
        res.append(path[:])
        return
    for choice in choices:
        path.append(choice)
        backtrack(path, new_choices)
        path.pop() # 復原狀態

```

### 12. Dynamic Programming (動態規劃 - 1D/Multi)

將大問題分解為子問題。

```python
def dp_1d(n):
    dp = [0] * (n + 1)
    dp[0], dp[1] = base_case
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2] # 狀態轉移方程
    return dp[n]

```

### 13. Graphs - DFS/BFS (圖論)

處理節點連通性。

```python
def graph_dfs(node, visited):
    if node in visited: return
    visited.add(node)
    for neighbor in adj[node]:
        graph_dfs(neighbor, visited)

```

### 14. Bit Manipulation (位元運算)

利用位元 XOR ($^$), AND ($\&$) 等特性。

```python
def bit_op(n):
    # 檢查最後一位是否為 1
    return n & 1
    # 去除最後一位 1
    # n = n & (n - 1)

```
這是在 LeetCode 75 中最後剩下的三個重要分類：**Trie (字典樹)**、**Monotonic Stack (單調堆疊)** 以及 **Intervals (區間)** 的 Python 通用模板。

---

### 15. Trie (字典樹 / 前綴樹)

主要用於處理字串集合，支援快速的插入與前綴搜尋。

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

---

### 16. Monotonic Stack (單調堆疊)

專門用來處理「找下一個更大/更小元素」的問題。核心在於維持堆疊內的元素遞增或遞減。

```python
def monotonic_stack(nums):
    stack = []  # 存儲索引 (index) 通常比存數值更靈活
    res = [-1] * len(nums)
    
    for i in range(len(nums)):
        # 以「單調遞增堆疊」為例：尋找右側第一個比自己大的元素
        # 當前元素比棧頂大，說明找到了棧頂元素的「下一個更大值」
        while stack and nums[i] > nums[stack[-1]]:
            index = stack.pop()
            res[index] = nums[i]
        
        stack.append(i)
    return res

```

---

### 17. Intervals (區間問題)

處理區間重疊、合併或插入。關鍵步驟通常是 **「按起點排序」**。

```python
def merge_intervals(intervals):
    if not intervals:
        return []
    
    # 步驟 1：按區間起點 (start) 進行排序
    intervals.sort(key=lambda x: x[0])
    
    merged = [intervals[0]]
    
    for i in range(1, len(intervals)):
        curr_start, curr_end = intervals[i]
        last_start, last_end = merged[-1]
        
        # 步驟 2：判斷是否有重疊 (當前起點 <= 前一個終點)
        if curr_start <= last_end:
            # 有重疊，合併區間（更新終點為兩者最大值）
            merged[-1][1] = max(last_end, curr_end)
        else:
            # 無重疊，直接加入結果
            merged.append([curr_start, curr_end])
            
    return merged

```


---




## 💾 空間複雜度排序表 (Space Complexity)

### 1. 常數空間 $O(1)$

除了輸入資料外，只用到固定數量的變數。

* **Two Pointers (雙指標)**：僅需兩個索引變數。
* **Bit Manipulation (位元運算)**：直接操作數值位元。
* **Array / String (原地操作)**：若題目要求 In-place 修改，則為 $O(1)$。
* **Binary Search (反覆運算版)**：僅需 `low`, `high`, `mid` 變數。

---

### 2. 線性空間 $O(n)$ 或 $O(m)$

空間消耗隨輸入資料量同步增加。這是大多數資料結構的標準消耗。

* **Array / String (複製版)**：若需建立新陣列儲存結果。
* **Hash Table / Set (雜湊表)**：儲存 $n$ 個鍵值對。
* **Stack / Queue (堆疊 / 佇列)**：最多存儲 $n$ 個元素。
* **Monotonic Stack (單調堆疊)**：最差情況需存入所有元素。
* **Linked List (鏈結串列)**：建立 $n$ 個節點。
* **Prefix Sum (前綴和)**：通常需要額外一個陣列儲存累加值。
* **Sliding Window (滑動視窗)**：若使用雜湊表記錄窗口內狀態，則為 $O(k)$，$k$ 為窗口大小。
* **Heap / Priority Queue (堆積)**：儲存 $n$ 個元素。
* **DP - 1D (一維動態規劃)**：儲存 $n$ 個狀態。
* **Trie (字典樹)**：總節點數隨總字元數增加。
* **Binary Tree / Graphs (遞迴 DFS)**：遞迴深度最差為 $O(n)$（斜向樹）。
* **Binary Tree / Graphs (BFS)**：佇列在最寬的一層可能達到 $O(n)$。

---

### 3. 多項式空間 $O(n \cdot m)$

* **DP - Multidimensional (多維動態規劃)**：建立二維矩陣（如 $dp[i][j]$）。
* **Graphs (鄰接矩陣)**：表示圖的連接關係需 $O(V^2)$。

---

### 4. 指數空間 $O(2^n)$ / $O(n!)$

* **Backtracking (回溯法)**：若要儲存所有可能的路徑或結果（如子集問題的所有組合）。

---

## 📊 綜合整理對照表

| 分類項目 | 時間複雜度 (Avg/Best) | 空間複雜度 (Auxiliary) | 備註 |
| --- | --- | --- | --- |
| **Two Pointers** | $O(n)$ | **$O(1)$** | 空間最省的技巧 |
| **Binary Search** | $O(\log n)$ | **$O(1)$** | 遞迴版為 $O(\log n)$ |
| **Hash Table** | $O(1)$ | **$O(n)$** | 用空間換時間的經典 |
| **DFS (Tree/Graph)** | $O(V+E)$ | **$O(H)$** | $H$ 為樹高（遞迴棧空間） |
| **BFS (Tree/Graph)** | $O(V+E)$ | **$O(W)$** | $W$ 為最大寬度（佇列空間） |
| **1D DP** | $O(n)$ | **$O(n)$** | 可透過「滾動陣列」優化至 $O(1)$ |
| **2D DP** | $O(n \cdot m)$ | **$O(n \cdot m)$** | 可優化至 $O(min(n, m))$ |
| **Trie** | $O(L)$ | **$O(N \cdot \Sigma)$** | $\Sigma$ 是字母表大小 |

---

」

1. **DP 優化**：如果 $dp[i]$ 只依賴 $dp[i-1]$，你可以只用兩個變數（空間從 $O(n) \to O(1)$）。
2. **原地 (In-place)**：檢查是否可以直接修改輸入的陣列，而不建立新陣列。


除了你列出的演算法分類，LeetCode 面試中還有幾個「隱形」的複雜度大戶，特別是內建函數（Built-in Functions）和特定的資料結構操作。

這裡補充 **排序 (Sorting)**、**容器操作 (Collections)** 以及一些常見的**進階場景**：

---

## 🏗️ 排序與搜尋 (Sorting & Searching)

這是最容易被忽略的，因為通常只寫一行 `.sort()`。

| 操作 | 時間複雜度 | 空間複雜度 | 備註 |
| --- | --- | --- | --- |
| **Quick/Merge Sort** | $O(n \log n)$ | $O(\log n)$ ~ $O(n)$ | Python `sort()` (Timsort) 空間為 $O(n)$ |
| **Built-in `Binary Search**` | $O(\log n)$ | $O(1)$ | 如 `bisect` (Python) 或 `lower_bound` (C++) |
| **K-th Element (Quick Select)** | $O(n)$ | $O(1)$ | 平均 $O(n)$，最差 $O(n^2)$ |

---

## 📦 常用容器操作 (Container Operations)

不同語言的實作略有差異，但 Big O 原則通用。

### 1. 列表 / 動態陣列 (List / Vector)

* **隨機存取 `arr[i]**`: $O(1)$
* **末端插入/刪除 `append`/`pop**`: $O(1)$ (均攤)
* **中間插入/刪除 `insert`/`delete**`: **$O(n)$** (因為要移動後方所有元素)
* **檢查元素是否存在 `x in arr**`: **$O(n)$** (線性搜尋)

### 2. 雙端佇列 (Deque)

* **兩端插入/刪除 `popleft`/`appendleft**`: $O(1)$
* **空間**: $O(n)$

### 3. 雜湊表 (HashMap / Dictionary)

* **尋找/插入/刪除**: $O(1)$ (最壞情況 $O(n)$ 但極少見)
* **空間**: $O(n)$

---

## 🧩 進階與特殊分類

### 1. 聯集查找 (Union Find / Disjoint Set)

常用於處理圖論的連通性問題。

* **時間**: $O(\alpha(n))$。$\alpha$ 是反阿克曼函數，實務上可視為 **$O(1)$**。
* **空間**: **$O(n)$** (需要一個 parent 陣列)。

### 2. 遞迴與字串處理 (String Manipulation)

* **字串拼接 (String Concatenation)**:
* 許多語言（如 Java/Python）字串是不可變的，`s + t` 的時間是 **$O(n + m)$**。
* **解法**: 面試建議用 `StringBuilder` 或將字元放入 List 最後再 `join`。


* **子字串切片 `s[i:j]**`: **$O(k)$**，其中 $k$ 為子字串長度。

### 3. 矩陣運算 (Matrix Operations)

* **矩陣遍歷**: $O(M \times N)$。
* **矩陣轉置 (Transpose)**: $O(M \times N)$ 時間，$O(1)$ 額外空間（若是方陣且原地修改）。

---

## 📈 總結：LeetCode 複雜度速查表

| 場景 | 時間 | 空間 | 常見題型 |
| --- | --- | --- | --- |
| **排序 (Sorting)** | $O(n \log n)$ | $O(n)$ | 合併區間、最大數 |
| **Union Find** | $O(1)^*$ | $O(n)$ | 島嶼數量、冗餘連接 |
| **Quick Select** | $O(n)$ | $O(1)$ | 第 K 個最大元素 |
| **String Build** | $O(n)$ | $O(n)$ | 復原 IP 地址、解碼字串 |
| **Binary Index Tree / Segment Tree** | $O(\log n)$ | $O(n)$ | 區域和檢索、動態修改 |

### 💡 專業面試提醒

在分析 **空間複雜度** 時，面試官通常想聽的是 **額外空間 (Auxiliary Space)**。例如，如果你回傳了一個答案陣列，那個陣列通常不計入空間複雜度，除非題目特別要求計算總空間。

需要我幫你把這些資訊與你原本的清單合併成一個完整的 **Markdown 懶人包表格** 嗎？


這份清單涵蓋了演算法面試中最核心的資料結構與演算法。我將其按照常見的**時間複雜度 (Time Complexity)** 進行分類與排序，並附上簡短的說明幫助你理解為什麼它們屬於該級別。

---

## 🚀 複雜度排序表 (Big O Complexity)

### 1. 常數時間 $O(1)$

這是最理想的情況，操作時間不隨資料量增加。

* **Bit Manipulation (位元運算)**：直接對二進位位元操作。
* **Hash Table / Set (雜湊表 / 集合)**：平均情況下的尋找、插入、刪除。
* **Stack / Queue (堆疊 / 佇列)**：基本的 `push`/`pop` 或 `enqueue`/`dequeue`。

---

### 2. 對數時間 $O(\log n)$

通常涉及將搜尋範圍折半的演算法。

* **Binary Search (二元搜尋)**：每次排除一半的範圍。
* **Binary Search Tree (二元搜尋樹)**：在平衡狀態下，搜尋與插入為對數時間。
* **Heap / Priority Queue (堆積)**：插入與移除最大/最小值（調整過程）。

---

### 3. 線性時間 $O(n)$

最常見的類別，通常需要遍歷一次資料。

* **Array / String (陣列 / 字串)**：基礎遍歷。
* **Two Pointers (雙指標)**：指標移動總計不超過 $2n$。
* **Sliding Window (滑動視窗)**：左右邊界各移動一次。
* **Prefix Sum (前綴和)**：預處理陣列。
* **Linked List (鏈結串列)**：遍歷或尋找節點。
* **Monotonic Stack (單調堆疊)**：每個元素進出堆疊各一次。
* **Trie (字典樹)**：尋找長度為 $L$ 的單字，時間與單字長度成正比（相對於資料集總量為線性）。
* **Intervals (區間)**：通常在排序後，線性遍歷處理重疊。
* **DP - 1D (一維動態規劃)**：計算 $n$ 個狀態。
* **Binary Tree / Graphs - DFS & BFS**：每個頂點與邊各訪問一次。

---

### 4. 擬線性時間 $O(n \log n)$

通常出現在需要**排序**的場景。

* **Intervals (區間)**：若原始資料未排序，通常需先排序。
* **Heap Sort**：利用 Heap 進行排序。

---

### 5. 多項式時間 $O(n^2)$ 或 $O(n \cdot m)$

* **DP - Multidimensional (多維動態規劃)**：例如二維 DP，狀態數為 $n \times m$。

---

### 6. 指數或階乘時間 $O(2^n)$ / $O(n!)$

* **Backtracking (回溯法)**：窮舉所有可能的排列組合。

---

## 💡 總結整理表

| 複雜度 | 資料結構 / 演算法 |
| --- | --- |
| **$O(1)$** | Bit Manipulation, Hash Table, Stack, Queue |
| **$O(\log n)$** | Binary Search, Heap (Insert/Delete), BST (Balanced) |
| **$O(n)$** | Array/String, Two Pointers, Sliding Window, Prefix Sum, Linked List, Monotonic Stack, Trie, 1D DP, Tree/Graph Traversal (DFS/BFS) |
| **$O(n \log n)$** | Sorting (often used in Intervals/Heap) |
| **$O(n \cdot m)$** | Multidimensional DP |
| **$O(2^n)$ / $O(n!)$** | Backtracking |

> **注意：** 空間複雜度 (Space Complexity) 會有所不同（例如 Hash Table 雖然查詢快，但需要 $O(n)$ 的空間）。

需要我針對其中哪一個特定的演算法（例如 **Monotonic Stack** 或 **Trie**）提供具體的程式碼範例或圖解嗎？