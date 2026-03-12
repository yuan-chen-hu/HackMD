# C++ 常用 
---

## 1. 必備標頭檔與基礎 (Boilerplate)

為了節省時間，競賽中常用「萬用標頭檔」。

* **萬用標頭檔**：`#include <bits/stdc++.h>` (包含所有標準庫)。
* **加速 I/O**：在 `main` 函式開頭加上這兩行，效能會接近 C 的 `scanf/printf`。
```cpp
ios::sync_with_stdio(0);
cin.tie(0);

```


* **常數定義**：`const int INF = 1e9;` 或 `const int MOD = 1e9 + 7;`。

---

## 2. 容器：Vector (動態陣列)

最常用的容器，相當於 Python 的 List。

* **宣告**：`vector<int> v(n, 0);` (長度為 n，初值為 0)。
* **操作**：
* `v.push_back(x)`：在尾端加入元素。
* `v.pop_back()`：刪除尾端元素。
* `v.size()`：取得長度。
* `v.clear()`：清空。


* **排序**：`sort(v.begin(), v.end());` (由小到大)。
* **反轉**：`reverse(v.begin(), v.end());`。

---

## 3. 容器：Map 與 Set (平衡樹/哈希)

用於快速尋找 ($O(\log n)$ 或 $O(1)$)。

* **`std::set` / `std::map**`：內部以紅黑樹實作，**會自動排序**。
* **`std::unordered_map`**：以 Hash Table 實作，不排序但**平均速度更快 ($O(1)$)**。
```cpp
unordered_map<string, int> mp;
mp["apple"] = 10;
if (mp.count("apple")) { // 檢查是否存在 }

```


* **`std::set` 常用技巧**：`s.insert(x)`、`s.erase(x)`。

---

## 4. 進階容器：Deque, Stack, Queue, Priority Queue

* **`deque<int> dq`**：雙向隊列，支援 `push_front` 與 `push_back`。
* **`priority_queue<int> pq`**：**優先權隊列 (堆積)**。
* 預設為**最大堆積** (Max-Heap)。
* 宣告最小堆積：`priority_queue<int, vector<int>, greater<int>> pq;`。
* 操作：`pq.push()`, `pq.pop()`, `pq.top()`。



---

## 5. 常用演算法函數 (Algorithms)

C++ 的 `<algorithm>` 庫非常強大：

* **二分搜尋 (Binary Search)**：
* `lower_bound(v.begin(), v.end(), x)`：找第一個 **≥ x** 的位置。
* `upper_bound(v.begin(), v.end(), x)`：找第一個 **> x** 的位置。


* **最大值/最小值**：
* `max(a, b)`、`min(a, b)`。
* `*max_element(v.begin(), v.end())`：找陣列中的最大值。


* **總和**：`accumulate(v.begin(), v.end(), 0);`。

---

## 6. 結構體與自定義排序 (Struct & Custom Sort)

當題目需要存多種屬性（如：學生的姓名與成績）時：

```cpp
struct Student {
    string name;
    int score;
};

// 自定義排序準則：分數高的在前，分數相同則名字字典序小的在前
bool cmp(Student a, Student b) {
    if (a.score != b.score) return a.score > b.score;
    return a.name < b.name;
}

sort(students.begin(), students.end(), cmp);

```

---

### 💡 小提醒：

在 C++ 中，**指標 (Pointer)** 和 **引用 (Reference, `&`)** 的概念很重要。在傳遞大型容器（如 `vector`）進函式時，記得用 `void func(vector<int>& v)` 加上 `&` 符號，才不會因為大量複製導致 TLE (Time Limit Exceeded)。

**需要我幫你寫一個特定演算法（例如「深度優先搜尋 DFS」或「最短路徑 Dijkstra」）的 C++ 模板嗎？**