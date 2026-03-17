# Git 常用指令

---

## 1. 開始一個專案（Setup）

當你要開始新工作或從雲端下載專案時使用。

```bash
git init                    # 在當前資料夾建立新的 Git 倉庫
git clone <url>             # 把遠端專案完整複製到本地
```

---

## 2. 存檔與記錄（Save & Track）

最頻繁使用的步驟，就像玩遊戲存檔一樣。**建議隨手 `git status`，避免出錯。**

```bash
git status                  # 檢查檔案狀態（哪些改了？哪些還沒存？）
git add <file>              # 將指定檔案加入暫存區（Staging Area）
git add .                   # 將所有變更加入暫存區
git commit -m "訊息"        # 正式提交暫存區內容，-m 後寫下本次變更說明
git log                     # 查看所有 Commit 紀錄
git log --oneline           # 簡潔版 log（每筆一行）
git log --oneline --graph   # 圖形化分支 log
```

---

## 3. 分支管理（Branching）

在不影響主線（main/master）的情況下開發新功能，分支是你的好朋友。

```bash
git branch                  # 列出所有本地分支
git branch <name>           # 建立新分支
git branch -d <branch>      # 刪除分支（已合併才能刪）
git branch -D <branch>      # 強制刪除分支

git checkout <branch>       # 切換分支
git checkout -b <branch>    # 建立並切換分支（懶人組合包）

git switch <branch>         # 切換分支（新語法，推薦）
git switch -c <branch>      # 建立並切換分支（新語法）

git merge <branch>          # 把指定分支合併到目前分支
```

---

## 4. 遠端同步（Remote Sync）

與團隊成員協作（GitHub / GitLab）時使用。

```bash
git push origin <branch>    # 推送本地分支到遠端
git push                    # 推送到預設遠端（已設定追蹤時）

git pull                    # 拉取遠端最新進度並自動合併（fetch + merge）
git fetch                   # 只去雲端查看更新，不自動合併（較安全）

git remote -v               # 查看目前設定的遠端倉庫
git remote add origin <url> # 新增遠端倉庫
```

---

## 5. 歷史與比較（History & Diff）

查看改了什麼、誰改的、改了哪裡。

```bash
git log --oneline           # 簡潔 log（每筆一行）
git log --oneline --graph   # 圖形化分支 log
git diff                    # 查看未暫存的變更（工作區 vs 暫存區）
git diff --staged           # 查看已暫存的變更（暫存區 vs 上次 commit）
git blame <file>            # 查看每一行最後是誰、哪次 commit 修改的
git show <commit>           # 查看某次 commit 的詳細內容
```

---

## 6. 撤銷與修復（Undo）

如果「慘了，改錯了」，這裡有後悔藥。

```bash
# 工作區撤銷（尚未 add）
git restore <file>          # 撤銷工作區某檔案的變更（回到上次 commit）
git checkout -- <file>      # 同上（舊語法）

# 暫存區撤銷（已 add，未 commit）
git restore --staged <file> # 取消暫存，退回工作區

# Commit 撤銷
git revert <commit>         # 新增一個「撤銷」的 commit（安全，適合已 push）
git reset --soft HEAD~1     # 撤銷最後一次 commit，保留變更於暫存區
git reset --hard HEAD~1     # 撤銷最後一次 commit，丟棄所有變更（不可逆！）
git reset --hard HEAD       # 放棄所有未 commit 的修改（小心使用）

# 暫存工作區（切換分支前臨時保存）
git stash                   # 把目前工作區的變更暫時藏起來
git stash pop               # 恢復最近一次 stash
git stash list              # 查看所有 stash
```

> ⚠️ `--hard` 為不可逆操作，執行前請確認。已 push 的 commit 建議用 `git revert` 代替 `reset`。

---

## 7. Tag 標籤（版本標記）

Tag 通常用來標記版本號（如 `v1.0.0`），分為兩種類型。

> **Lightweight vs Annotated Tag 差異：**
> - **Lightweight**：只是一個指向 commit 的指標，無額外資訊。
> - **Annotated**：包含標籤者、日期、說明訊息，**正式版本建議使用**。

### 建立標籤

```bash
# 輕量標籤（Lightweight）
git tag v1.0.0              # 在當前 commit 建立
git tag v1.0.0 <commit>     # 在指定 commit 建立

# 附註標籤（Annotated，推薦）
git tag -a v1.0.0 -m "Release version 1.0.0"
git tag -a v1.0.0 <commit> -m "說明訊息"
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
git checkout v1.0.0                     # 切換到標籤（唯讀狀態）
git checkout -b fix/v1.0.0 v1.0.0       # 從標籤建立新分支
```

---

## 8. Commit 訊息規範（Conventional Commits）

良好的 commit 訊息格式：`<type>: <簡短說明>`

| Type | 用途 | 影響版本號 |
| --- | --- | --- |
| `feat` | 新增功能 | MINOR |
| `fix` | 修復 Bug | PATCH |
| `docs` | 更新文件（README、API 文件等） | 無 |
| `style` | 格式調整（縮排、逗號等），不影響邏輯 | 無 |
| `refactor` | 重構程式碼，未新增功能也未修正 Bug | 無 |
| `test` | 新增或修改測試檔案 | 無 |
| `chore` | 雜項工作（工具設定、依賴更新等） | 無 |
| `build` | 編譯過程相關修改（webpack、rollup 等） | 無 |
| `ci` | CI/CD 腳本或流程相關更動 | 無 |
| `perf` | 效能優化 | 無 |
| `revert` | 回退某次提交 | 視情況 |

**範例：**

```
feat: 新增使用者登入功能
fix: 修正首頁圖片載入失敗問題
docs: 更新 README 安裝說明
refactor: 將 auth 模組拆分為獨立服務
```
