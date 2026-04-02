# CI 體驗專案：GitHub Actions 自動跑測試

這是一個搭配文章《CI 是什麼？Junior 工程師也能跟著體驗的 GitHub Actions 流程》的練習專案。

透過這個 Laravel 專案，你可以親手體驗一次完整的 CI 流程：push 程式碼 → GitHub Actions 自動跑測試 → 看到綠色勾勾（或紅色叉叉）。

---

## 事前準備

- 一個 GitHub 帳號
- 本機已安裝 [Git](https://git-scm.com/)
- 本機已安裝 [PHP 8.1+](https://www.php.net/) 和 [Composer](https://getcomposer.org/)（想在本機跑測試才需要，純體驗 CI 可跳過）

---

## 開始體驗

### Step 1：Fork 這個專案

到 GitHub 上點右上角的 **Fork**，把這個 repo 複製一份到你自己的帳號下。

### Step 2：Clone 到本機

```bash
git clone https://github.com/<你的 GitHub 帳號>/cicd-learning.git
cd cicd-learning
```

### Step 3：安裝依賴並設定環境

```bash
composer install
cp .env.example .env
php artisan key:generate
```

### Step 4：在本機跑一次測試（確認環境正常）

```bash
php artisan test
```

你應該會看到測試通過的結果。

### Step 5：推上 GitHub，觸發 CI

目前專案裡已經有 `.github/workflows/ci.yml`，所以只要 push 到 `main` 分支，GitHub Actions 就會自動執行。

```bash
git push origin main
```

### Step 6：到 GitHub 看結果

1. 打開你 fork 的 repo 頁面
2. 點上方的 **Actions** 分頁
3. 你會看到一個正在跑（或已完成）的 workflow
4. 全部通過 → commit 旁邊出現綠色勾勾

---

## 體驗測試失敗的情況

試著讓測試故意失敗，看看 CI 會怎麼反應：

1. 打開 `tests/Feature/ExampleTest.php`
2. 把 `assertStatus(200)` 改成 `assertStatus(500)`
3. Commit 並 push：

```bash
git add tests/Feature/ExampleTest.php
git commit -m "test: 故意讓測試失敗來體驗 CI"
git push origin main
```

4. 回到 GitHub 的 Actions 頁面，你會看到紅色叉叉
5. 點進去看 log，找到 `Run tests` 那一步，就能看到失敗原因

記得體驗完把測試改回來！

---

## 專案結構（跟 CI 相關的部分）

```
.github/workflows/ci.yml   ← CI 的設定檔，定義了自動化流程
tests/Feature/              ← Feature 測試
tests/Unit/                 ← Unit 測試
phpunit.xml                 ← PHPUnit 設定檔
```

---

## CI 設定檔說明

`.github/workflows/ci.yml` 做了這些事：

1. 當 `push` 到 `main` 或有 PR 指向 `main` 時觸發
2. 在 Ubuntu 虛擬機上設定 PHP 8.1 環境
3. 安裝 Composer 依賴
4. 複製 `.env` 並產生 APP_KEY
5. 用記憶體內的 SQLite 跑測試

詳細的概念說明請參考文章本體。

---

## 延伸挑戰

如果你想多玩一點：

- 試著開一個 PR（而不是直接 push 到 main），看看 PR 頁面底部的 checks 區塊
- 到 repo 的 **Settings → Branches → Add rule**，開啟 Branch Protection，強制要求 CI 通過才能 merge
- 試著在 `tests/` 裡新增自己的測試案例，看 CI 會不會一起跑
