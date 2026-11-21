# 變更紀錄

版本對應 `apps/api/app/version.py` 的 `APP_VERSION`。
格式參考 [Keep a Changelog](https://keepachangelog.com/zh-TW/1.1.0/)。
行為有變就在 `[Unreleased]` 留一筆；發版用 `make release <版號>`，不要手動改版號。

## [Unreleased]

- 排程不再在整分鐘前的最後 1 秒提早觸發（例如每天 00:00 的排程在前一天 23:59:59 執行）；任務的執行時間以資料庫的時鐘為準。
- 移除 Sentry：不再讀 `SENTRY_DSN`，主機 `.env` 裡的這一行可以刪掉。
- 角色裡殘留已不存在的權限值時，登入不再回 500；並行的改密碼請求不再耗盡資料庫連線。
- 長駐的服務改成 `restart: unless-stopped`：部署或還原失敗後刻意停下的服務，在 Docker 重啟後維持停止。
  `make start` 等其他服務都就緒才開放入口。
- 備份前先停下所有會寫資料的服務，備份目錄只讓擁有者進入。新增 `make backup restore all` 一次還原同一組的資料庫與上傳的檔案；
  **還原資料庫之後所有人都要重新登入**，還原前簽發的登入憑證一律失效。
- 部署只接受 `main` 上的 commit；從草稿發布的 pre-release 也會部署。部署流程用的指令集中到 `make pipeline`，
  首次部署在 migration 之前失敗後可以重新部署。
- `make doctor` 檢查主機的 reverse proxy 與憑證剩餘天數，`make status` 列出 volume，`make help` 依目前狀態顯示可用的指令。
- 前端的錯誤回報有數量上限，被丟掉時在 log 留一行摘要。
- PostgreSQL 升到 18.7（同一個大版本，不用搬資料）。

## [0.2.1]

- 修正後台管理使用者的安全問題：帳號身上停用的角色也算進管理資格（避免角色恢復後被低權限管理者接管）；
  停用帳號時登入版本由資料庫遞增，不會讓已撤銷的登入恢復；重設密碼在寫入前再確認一次能不能管理對方。
- `make db superuser`、`make db revoke-sessions` 不再在終端機印出 AUDIT 的 log，紀錄照樣寫進 `audit_logs`。

## [0.2.0]

- `.env` 的 `API_WORKERS` 改名為 `API_PROCESSES`；api 的記憶體上限依行程數自動調整（1 個 512M，每多一個加 256M）。
  **部署前主機的 `.env` 要照樣改名**，沒改時用預設的 1 個行程。
- 新增 `WORKER_REPLICAS`（預設 1）可以開多份 worker 並行處理任務；`API_PROCESSES` 與 `WORKER_REPLICAS`
  加起來最多 6，超過時 `make start` 拒絕啟動。
- worker 的容器名稱變成 `<專案>-worker-<序號>`（例如 `project-test-worker-1`），升級時舊的 `<專案>-worker` 會被移除。
- 背景任務的時間上限最多 300 秒，`JobSpec(timeout_seconds=...)` 超過時 worker 啟動就報錯。

## [0.1.1]

- 修正部署：主機上的部署腳本不再被中途的指令讀走，備份、切換版本、清理舊映像檔與舊備份每一步都會執行。
- `make logs trace`、`make logs errors` 在帳號讀不到 journald 時會直接說明原因；`make doctor` 多一項「log 查詢」。
  部署主機上執行這些指令的帳號要在 `systemd-journal` 群組。

## [0.1.0]

- 記下 foundation 的同步點（`.foundation.json`），之後可以用 `sync-foundation` 帶進骨架的更新。

## [0.0.0]

- 專案初始化：FastAPI + Next.js 全端骨架，含登入、使用者與角色權限、背景任務與排程、中英雙語。
