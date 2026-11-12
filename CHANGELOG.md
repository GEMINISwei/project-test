# 變更紀錄

版本對應 `apps/api/app/version.py` 的 `APP_VERSION`。
格式參考 [Keep a Changelog](https://keepachangelog.com/zh-TW/1.1.0/)。
行為有變就在 `[Unreleased]` 留一筆；發版用 `make release <版號>`，不要手動改版號。

## [Unreleased]

- 修正部署：主機上的部署腳本不再被中途的指令讀走，備份、切換版本、清理舊映像檔與舊備份每一步都會執行。
- `make logs trace`、`make logs errors` 在帳號讀不到 journald 時會直接說明原因；`make doctor` 多一項「log 查詢」。
  部署主機上執行這些指令的帳號要在 `systemd-journal` 群組。

## [0.1.0]

- 記下 foundation 的同步點（`.foundation.json`），之後可以用 `sync-foundation` 帶進骨架的更新。

## [0.0.0]

- 專案初始化：FastAPI + Next.js 全端骨架，含登入、使用者與角色權限、背景任務與排程、中英雙語。
