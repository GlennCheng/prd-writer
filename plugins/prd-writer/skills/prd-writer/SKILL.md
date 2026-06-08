---
name: prd-writer
description: 互動式 PRD（產品需求文件）起草 skill。當使用者說「幫我寫 PRD」、「建立需求文件」、「write PRD」、「create PRD」、「feature spec」、「product requirements」、「需求規格」，或提供任何功能描述、Jira ticket ID 想要起草需求文件時，必須使用此 skill。支援從對話上下文、Ticket ID、或自由描述三種方式啟動。核心特色：AI 扮演指導教授角色，先與 PM 腦力激盪確認方向，再透過動態 Clarify 互動深度挖掘需求，產出結構化 PRD。任何想寫 PRD、spec、需求規格、功能規劃的情境都應觸發此 skill。
---

# PRD Writer

## 設計哲學

> **AI 是指導教授，不是執行機器。**
>
> 人類帶著任務來，AI 先跟你一起腦力激盪，確認問題設定正確——  
> 因為人類往往帶著解法來，卻忘了先確認問題本身對不對。  
> 方向確認後，再進入結構化的 PRD 起草流程。

---

## Step 0：初始化

1. 顯示 Banner（見下方）
2. 掃描對話記憶體——若混雜不相干任務，警告建議開新 Chat
3. 往上層尋找專案根目錄（`.git` / `package.json` / `pyproject.toml`）
4. 讀取 `.prd-writer.yml`（若存在）；**第一次執行**則詢問 PRD 儲存位置後建立
5. 偵測可用 MCP：Jira（Atlassian）、Confluence、Notion、GitHub——有什麼用什麼，無則繼續

**Banner：**
```text
  ____  ____  ____    _       __   _ __           
 / __ \/ __ \/ __ \  | |     / /  (_) /____  _____
/ /_/ / /_/ / / / /  | | /| / / / / __/ _ \/ ___/
/ ____/ _, _/ /_/ /   | |/ |/ / / / /_/  __/ /    
/_/   /_/ |_/_____/    |__/|__/_/_/\__/\___/_/     
```

---

## Step 1：狀態偵測

檢查 `<prd_root>/<feature-slug>/state.yml`：

| 狀態 | 條件 | 行為 |
|------|------|------|
| **NEW** | 草稿不存在 | 進入 Step 2-0 開場腦力激盪 |
| **DRAFTING** | `phase: drafting` | 讀取進度，從中斷處繼續 Step 2 |
| **FINALIZED** | `phase: finalized` | 詢問：發布到某處 or 繼續修改 |
| **PUBLISHED** | `phase: published` | 衝突偵測後進入修改模式 |

初始化 `state.yml`（NEW 時）：
```yaml
feature: <slug>
ticket: <TICKET_ID 或 null>
phase: drafting
created_at: <timestamp>
updated_at: <timestamp>
clarify_rounds: 0
prd_version: 0
published_to: []
```

---

## Step 2：Clarify 互動

**載入 `references/clarify-engine.md`**，依照其中的完整規則執行。

### 2-0：開場腦力激盪

AI 先主動發言（不是問問題）——分析任務描述後說出觀察、疑問、替代方向。  
雙向對話確認方向後，才進入 2-1。

### 2-1：初始評估（2-3 題）

快速推斷 context：內部工具 / 外部產品 / 技術基建？規模？受眾頻率？  
根據結果動態決定後續問題的深度與維度重心。

### 2-2：問題合成互動（滾雪球）

- AI 內部掃描全部維度，找張力點
- 合成問題：一題觸碰 2-5 個維度
- 每個回答更新 AI 內部模型，動態決定下一題
- 全程持續偵測解法建議（不直接寫成 FR）

### 2-3：每輪後 Checkpoint（強制，不可跳過）

展示覆蓋率，PM 說「定稿」/「夠了」/「done」才進入 Step 3。

**每輪結束後立即持久化：**
- 更新 `clarify-log.md`
- 更新 `prd.md` 對應區塊
- 更新 `state.yml`

---

## Step 3：PRD 產出 + 品質驗證

**載入 `references/prd-template.md`** 作為結構參照。  
**載入 `references/quality-check.md`** 執行驗證。

1. 根據所有 Clarify 資訊填充 PRD 各區塊
2. 不清楚但影響小 → Assumptions 記錄；影響大 → `[NEEDS CLARIFICATION]`（最多 3 個）
3. 品質驗證：列出警告，問 PM 要修正哪些（不強制）
4. 矛盾偵測：列出衝突，讓 PM 逐一決定
5. 展示完整 PRD → PM 確認 → `phase: finalized`

---

## Step 4：發布

**載入 `references/publish-adapters.md`**，依照其中的規則執行。

```
選擇發布目的地（可多選，可略過）：
📄 本地 markdown   → 已存在，完成
🔷 Confluence      → 若有 Atlassian MCP
📝 Notion          → 若有 Notion MCP
🐙 GitHub          → 若有 GitHub MCP
📋 純複製全文       → 展示全文，手動貼
```

發布後更新 `state.yml` 的 `published_to` 和 `phase: published`。  
記錄工具設定到 `.prd-writer.yml`。

---

## 本地儲存結構

```
<prd_root>/<feature-slug>/
├── state.yml          ← 狀態追蹤
├── jira-snapshot.md   ← Jira 快照（若有 Ticket）
├── clarify-log.md     ← Clarify 問答記錄
└── prd.md             ← PRD 文件（即時更新）
```
