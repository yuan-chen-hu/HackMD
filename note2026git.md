# Git 常用指令


---

## 1. 開始一個專案 (Setup)

當你要開始新工作或從雲端下載專案時使用。

* `git init`：在當前資料夾建立一個新的 Git 倉庫（Repo）。
* `git clone [URL]`：把遠端的專案整個「複製」一份到你的電腦。

---

## 2. 存檔與記錄 (Save & Track)

這是最頻繁使用的步驟，就像玩遊戲存檔一樣。

* **`git status`**：檢查目前檔案的狀態（哪些改了？哪些還沒存？）。**建議隨手輸入，避免出錯。**
* `git add [檔案名稱]`：把檔案加入「暫存區」（Staging Area）。如果你想全加，可以用 `git add .`。
* `git commit -m "你的訊息"`：正式把暫存區的東西存進資料庫，`-m` 後面要寫下這次改了什麼。
* `git log`：查看過去所有的存檔（Commit）紀錄。

---

## 3. 分支管理 (Branching)

要在不影響主線（Main/Master）的情況下開發新功能，分支是你的好朋友。

* `git branch`：列出目前所有的分支。
* `git branch [分支名稱]`：新增一個分支。
* **`git checkout [分支名稱]`**：切換到該分支。
* `git checkout -b [分支名稱]`：**懶人組合包**，直接「新增並切換」到該分支。
* `git merge [來源分支]`：把別的分支的內容合併到目前的分支。

---

## 4. 遠端同步 (Remote Sync)

當你要跟團隊成員協作（例如使用 GitHub 或 GitLab）時。

* `git push`：把你的本地存檔推送到雲端。
* `git pull`：把雲端的最新進度拉下來，並自動跟你的程式碼合併。
* `git fetch`：只去雲端看有什麼更新，但不急著合併。

---

## 5. 救命指令 (Undo)

如果你發現「慘了，改錯了」，這裡有後悔藥：

* `git checkout -- [檔案名稱]`：放棄對某個檔案的修改，回到上次存檔的樣子。
* `git reset --hard HEAD`：**大絕招**，直接放棄所有未存檔的修改，回到最新的一次 Commit（小心使用，這不可逆）。
* `git revert [Commit ID]`：用一個「新的存檔」來抵銷舊的存檔（適合已經 Push 到雲端的情況）。

---

# 常用 Git 指令

## 基本操作

```bash
git init                    # 初始化專案
git clone <url>             # 克隆遠端儲存庫
git status                  # 查看工作區狀態
git add .                   # 暫存所有變更
git add <file>              # 暫存指定檔案
git commit -m "訊息"        # 提交變更
git push origin <branch>    # 推送到遠端
git pull                    # 拉取並合併遠端變更
git fetch                   # 拉取遠端變更（不合併）
```

## 分支管理

```bash
git branch                  # 列出所有本地分支
git branch <name>           # 建立新分支
git checkout <branch>       # 切換分支
git checkout -b <branch>    # 建立並切換分支
git switch <branch>         # 切換分支（新語法）
git switch -c <branch>      # 建立並切換分支（新語法）
git merge <branch>          # 合併分支
git branch -d <branch>      # 刪除分支
git branch -D <branch>      # 強制刪除分支
```

---

## 🏷️ Tag 標籤（重點補充）

Tag 通常用來標記版本號（如 `v1.0.0`），分為兩種類型：

### 輕量標籤 Lightweight Tag
```bash
git tag v1.0.0              # 在當前 commit 建立輕量標籤
git tag v1.0.0 <commit-hash># 在指定 commit 建立輕量標籤
```

### 附註標籤 Annotated Tag（推薦）
```bash
git tag -a v1.0.0 -m "Release version 1.0.0"           # 建立附註標籤
git tag -a v1.0.0 <commit-hash> -m "說明訊息"           # 在指定 commit 建立
```

### 查看標籤
```bash
git tag                     # 列出所有標籤
git tag -l "v1.*"           # 用萬用字元篩選標籤
git show v1.0.0             # 查看標籤詳細資訊
git log --oneline --decorate# 在 log 中顯示標籤位置
```

### 推送標籤到遠端
```bash
git push origin v1.0.0      # 推送單一標籤
git push origin --tags      # 推送所有標籤
```

### 刪除標籤
```bash
git tag -d v1.0.0                       # 刪除本地標籤
git push origin --delete v1.0.0         # 刪除遠端標籤
git push origin :refs/tags/v1.0.0       # 刪除遠端標籤（舊語法）
```

### 切換到標籤（Detached HEAD）
```bash
git checkout v1.0.0         # 切換到標籤（唯讀狀態）
git checkout -b fix/v1.0.0 v1.0.0  # 從標籤建立新分支
```

---

## 歷史與比較

```bash
git log --oneline           # 簡潔 log
git log --oneline --graph   # 圖形化分支 log
git diff                    # 查看未暫存的變更
git diff --staged           # 查看已暫存的變更
git blame <file>            # 查看每行的提交者
```

## 撤銷與修復

```bash
git restore <file>          # 撤銷工作區變更
git restore --staged <file> # 取消暫存
git revert <commit>         # 新增一個「撤銷」的 commit
git reset --soft HEAD~1     # 撤銷最後一次 commit（保留變更）
git reset --hard HEAD~1     # 撤銷最後一次 commit（丟棄變更）
git stash                   # 暫存當前工作區
git stash pop               # 恢復暫存的工作區
```

---

> **Lightweight vs Annotated Tag 差異：**
> - Lightweight：只是一個指向 commit 的指標，無額外資訊
> - Annotated：包含標籤者、日期、說明訊息，建議正式版本使用

commit 習慣

feat： 表示新增了一個新的功能。通常會影響 MINOR 版本號。
fix： 用來描述修復的問題。通常會影響 PATCH 版本號。
docs： 任何文檔的更新，例如 README 或 API 文件的修改。不會影響程式碼執行。
style： 只涉及程式碼的格式化，與程式邏輯無關的修改，比如縮排、逗號位置等。
refactor： 改善程式結構或優化，未新增功能，也沒有修正 Bug。
test： 專門針對測試檔案的修改。
chore： 一些雜項工作，例如工具設定、依賴更新等。
build： 涉及編譯過程的修改，例如修改 webpack、rollup 等配置。
ci： 與 CI/CD 腳本或流程有關的更動。
perf： 專注於提升執行效能的修改。
revert： 如果需要回退某次提交，使用 revert。
