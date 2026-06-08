# Publish Adapters

## 執行時機

Step 4，PRD `phase: finalized` 後執行。  
PM 可多選、可略過任何選項。

---

## 選項選單

```
✅ PRD 已定稿，儲存於：<path>/prd.md

選擇發布目的地（可多選，可略過）：
1️⃣  📄 本地 markdown   — 已存在，完成
2️⃣  🔷 Confluence      — 需要 Atlassian MCP
3️⃣  📝 Notion          — 需要 Notion MCP
4️⃣  🐙 GitHub          — 需要 GitHub MCP
5️⃣  📋 純複製全文       — 展示全文，手動貼到任何地方
0️⃣  略過              — 本地保存即可
```

偵測到對應 MCP 時才顯示該選項；未偵測到的 MCP 選項以灰色說明顯示（不隱藏，說明如何啟用）。

---

## Confluence

**前置條件：** Atlassian MCP 可用（`mcp__atlassian__*` 工具存在）

**首次發布：**
```
使用 createConfluencePage：
  spaceId = .prd-writer.yml 的 confluence.space_id
  parentId = .prd-writer.yml 的 confluence.parent_page_id（若有）
  title = "[TICKET_ID] 功能名稱 — PRD"（有 Ticket）
          "功能名稱 — PRD"（無 Ticket）
  body = prd.md 內容
  contentFormat = "markdown"
```

**更新發布（已有 page_id）：**
```
使用 updateConfluencePage：
  pageId = state.yml 的 confluence_page_id
  versionMessage = "Updated via PRD Writer"
```

**衝突處理：** 若 Confluence 版本比 state.yml 記錄的新，展示差異讓 PM 選擇：
- 以 Confluence 版本為準
- 以本地版本為準
- AI 協助合併

**發布後更新 state.yml：**
```yaml
published_to:
  - confluence:
      page_id: <id>
      url: <url>
      last_version: <n>
      published_at: <timestamp>
```

**MCP 失敗降級：**
```
⚠️ 無法連接 Confluence。
選擇：
1️⃣ 稍後重試（保持 finalized 狀態）
2️⃣ 改用「純複製全文」方式
```

---

## Notion

**前置條件：** Notion MCP 可用

依照 Notion MCP 的工具定義執行 page 建立或更新。  
記錄 page_id 和 url 到 state.yml。

---

## GitHub

**前置條件：** GitHub MCP 可用（`mcp__plugin_github_github__*` 工具存在）

**行為：**
- 將 `prd.md` commit 到 `.prd-writer.yml` 設定的 branch 和 path
- commit message：`docs: add PRD for [功能名稱]`（首次）/ `docs: update PRD for [功能名稱]`（更新）

**發布後更新 state.yml：**
```yaml
published_to:
  - github:
      commit_sha: <sha>
      path: <path>
      published_at: <timestamp>
```

---

## 純複製全文

展示 PRD 完整 markdown 內容，PM 可手動貼到任何地方（Confluence、Notion、Google Docs、email 等）。

---

## 更新 .prd-writer.yml

每次成功發布後，將使用到的工具設定記錄到 `.prd-writer.yml`，下次不用重填：

```yaml
integrations:
  confluence:
    space_id: <space_id>
    parent_page_id: <parent_id>
  github:
    branch: main
    path: docs/prd
```
