# PRD Writer Skill — 設計文件

**日期：** 2026-06-06  
**狀態：** 已確認，待實作  
**工作代號：** `prd-writer`（名字 TBD，候選：Specwright、Speqio、Prodraft）

---

## 設計哲學

> **AI 是指導教授，不是執行機器。**
>
> 一個好的 PhD 指導教授不會只幫學生整理論文——他會問「你的研究問題本身設定對嗎？」  
> 他有更廣的視野，見過夠多案例，知道這條路走下去會碰到什麼。  
> 他挑戰前提假設，但尊重學生的自主決策。點出盲點後，讓學生自己選擇怎麼走。
>
> 這個 skill 的 AI 角色：**思維夥伴 + 方向挑戰者 + PRD 起草助理**。  
> 人類帶著任務來，AI 先跟你一起腦力激盪，確認問題設定正確，再進入結構化的 PRD 起草流程。

---

## 目標與定位

- **目標使用者：** 任何需要起草 PRD 的人（PM、工程師、創業者、個人開發者）
- **工具依賴：** 零依賴。偵測到 Jira / Confluence / Notion / GitHub MCP 時自動加成
- **語言：** 跟著使用者說話的語言自動切換
- **長期願景：** 未來擴充支援 RD 工作流（`prd-writer.dev`）、甚至成為獨立平台

---

## 架構：模組化單一 Skill（方案 B）

```
prd-writer/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── prd-writer/
        ├── SKILL.md                  ← 主流程（輕薄編排層，~150 行）
        └── references/
            ├── clarify-engine.md     ← Clarify 輪次邏輯、問題合成原則
            ├── prd-template.md       ← PRD 結構模板（可擴充）
            ├── quality-check.md      ← 品質驗證與矛盾偵測規則
            └── publish-adapters.md   ← 各平台發布方式
```

**設計原則：**
- SKILL.md 只負責主流程編排，細節按需載入 references
- 每個 reference 可獨立更新，不影響主流程
- 未來加 RD skill 時不需要動 PM skill

---

## 核心流程

### Step 0：初始化

1. 顯示品牌 Banner（TBD，設計感強的 ASCII art）
2. 偵測對話上下文（是否有正在進行的功能討論可作為起點）
3. 找到專案根目錄（往上尋找 `.git` / `package.json` 等）
4. 讀取 `.prd-writer.yml`（若存在）
5. 偵測可用 MCP（Jira、Confluence、Notion、GitHub 等）
6. **第一次在此專案執行**：詢問 PRD 儲存位置，建立 `.prd-writer.yml`

**`.prd-writer.yml` 結構：**
```yaml
prd_root: docs/prd
language: zh-TW             # 或 auto（跟著使用者語言）
integrations:
  confluence:
    space_id: ~
    parent_page_id: ~
  github:
    branch: main
    path: docs/prd
  # 未來偵測到新工具自動新增
```

---

### Step 1：狀態偵測

| 狀態 | 條件 | 行為 |
|------|------|------|
| NEW | 草稿目錄不存在 | 進入開場腦力激盪 |
| DRAFTING | `phase: drafting` | 讀取進度，從中斷處繼續 |
| FINALIZED | `phase: finalized` | 詢問要發布還是繼續修改 |
| PUBLISHED | `phase: published` | 衝突偵測後進入修改模式 |

**`state.yml` 結構：**
```yaml
feature: user-login-flow
ticket: PROJ-123            # 若有 Ticket，否則 null
phase: drafting
created_at: 2026-06-06
updated_at: 2026-06-06
clarify_rounds: 3
prd_version: 2
published_to:
  - confluence: https://...
  - github: https://...
```

---

### Step 2：Clarify 互動（核心）

載入 `references/clarify-engine.md`。

#### 2-0：開場腦力激盪（先說，再問）

AI 先主動發言，不是問問題：

> 「我看了你說的這個需求，有幾個想法想先聊聊——  
>  這個問題的背景看起來是 X，但我也注意到 Y 可能是更根本的問題。  
>  另外 Z 這個方向有沒有考慮過？  
>  我們先聊聊再決定往哪個方向寫 PRD。」

目的：
- 挖掘背景故事（誰提出的？為什麼是現在？）
- 評估任務描述是「問題」還是「解法」
- 若是解法 → 探索是否有更好的方向
- 雙向對話，PM 可同意、反駁、補充
- 方向確認後才進入結構化 Clarify

#### 2-1：初始評估（2-3 題）

快速推斷 context，決定後續問題深度：
- 內部工具 vs 外部產品 vs 技術基建？
- 功能規模（小改動 / 中等功能 / 大系統）？
- 受眾範圍與使用頻率？

#### 2-2：維度掃描（AI 內部，不輸出）

以下為**思考起點**，AI 可自行擴充或移除：

| 類別 | 維度 |
|------|------|
| 需求核心 | 目的、使用者、行為流程、上下游依賴 |
| 完整性 | 衍生需求、相關需求、漏掉的角色 |
| 現實情境 | 真實 vs 預期使用、極端情境、現實限制 |
| 擴充彈性 | 未來成長方向、現在決策是否堵住未來 |
| 優缺取捨 | 不同方向 trade-off、成本效益 |
| 使用者心理 | 情緒狀態、認知負荷、動機與阻力、損失規避、預期 vs 體驗落差 |
| 行為心理 | 習慣迴路、Job-to-be-Done、決策疲勞點 |
| 社會心理 | 社會認同、信任建立、群體動態、誰影響誰 |
| 二階效應 | 功能推出後行為改變帶來的新需求或問題 |
| 連鎖反應 | 對其他功能、流程、團隊的影響 |
| 時間維度 | 使用時態（事前/中/後）、學習曲線、市場時機 |
| 利害關係人 | 除終端使用者外誰被影響、內部阻力、決策鏈 |
| 注意力經濟 | 認知負荷成本、中斷成本、使用頻率影響 |
| 可觀測性 | 上線後追蹤哪些事件、失敗的早期訊號 |
| 風險與道德 | 誤用風險、隱私資料、可及性 |
| 脈絡情境 | 使用環境、競爭脈絡、替代方案 |

**重要：** 這是認知鷹架，不是執行清單。AI 用它起跳，但可以飛到清單以外的地方。

#### 2-3：問題合成與互動（滾雪球式）

**核心原則：**
- AI 先掃描全部維度，找出張力點與盲點
- 合成問題：一個問題同時觸碰 2-5 個維度
- 每個回答都更新 AI 的內部模型
- 下一個問題由對話動態決定，不是固定序列
- 解法建議偵測：全程持續，不直接寫成 FR

**問題卡片格式（Modern Dashboard 風格）：**

多選題：
```
> 🎯 **PRD Writer** | <功能名稱>
> 
> ---
> 
> 💡 **<維度>釐清 (Round N - Q X/Y)：<問題>**
> 
> **可選方案：**
> - 1️⃣ **選項一** — 說明 | ⚙️ 成本 [S/M/L]
> - 2️⃣ **選項二** — 說明 | ⚙️ 成本 [S/M/L]
> - 3️⃣ **選項三** — 說明 | ⚙️ 成本 [S/M/L]
> 
> 🏆 **AI 推薦：** <推薦與理由>
> 
> ⚖️ **影響分析：**
> - 🧑‍💻 工程端：...
> - 👤 使用者：...
> - 🧑‍💼 業務端：...
> 
> 💬 *回覆選項數字，或輸入你的考量。*
```

#### 2-4：Checkpoint（每輪後，強制執行）

```
> 📊 **Clarify Checkpoint** | Round N 完成
> 
> - ✅ 已釐清：<確認的面向>
> - ❓ 尚未確認：<缺少的面向>
> 
> 📈 當前覆蓋率：X / Y 主要面向
> 
> 繼續 → 下一輪 | 定稿 → 進入 PRD 產出
```

---

### Step 3：PRD 產出與品質驗證

載入 `references/prd-template.md` + `references/quality-check.md`。

**PRD 結構（specrity 原版 + 可擴充）：**
- Story Background（mandatory）
- User Scenarios & Testing（mandatory）
- Scope（mandatory）
- Requirements（mandatory）
- Experience Expectations（optional）
- Success Criteria（mandatory）
- Clarifications（auto-populated）
- Assumptions（optional）
- 原始解法建議（reference only）
- **+ AI 在 Clarify 過程中建議的額外區塊**（如 Security、Analytics、Compliance 等）

**品質驗證（輕量，提示不強制）：**

| 檢查項目 | 若不合格 |
|---------|---------|
| 完整性 | 列出缺少的區塊，問是否補充 |
| 清晰度 | 標出模糊詞，建議量化 |
| 可測性 | 標出無法客觀驗證的 FR/SC |
| PM 語言 | 標出技術術語，建議轉譯 |
| 矛盾偵測 | 列出衝突，問如何解決 |

**矛盾類型：** 範圍矛盾、需求衝突、優先級矛盾、指標矛盾、假設衝突

---

### Step 4：發布

載入 `references/publish-adapters.md`。

```
選擇發布目的地（可多選，可跳過）：

📄 本地 markdown    → 已存在，完成
🔷 Confluence       → Atlassian MCP → createConfluencePage
📝 Notion           → Notion MCP    → 建立 page
🐙 GitHub           → GitHub MCP    → commit
📋 純複製全文        → 展示全文，手動貼
```

發布後記錄到 `state.yml` 和 `.prd-writer.yml`。

---

## 本地儲存結構

```
<prd_root>/<feature-slug>/
├── state.yml           ← 狀態追蹤（phase、版本、發布記錄）
├── jira-snapshot.md    ← Jira 需求快照（若有 Ticket）
├── clarify-log.md      ← Clarify 問答完整記錄
└── prd.md              ← PRD 文件（即時更新）
```

---

## 設計決策記錄

| 決策 | 選擇 | 理由 |
|------|------|------|
| 架構 | 模組化單一 skill | 維護性 + 擴充性平衡，未來自然演進成多 skill |
| Clarify 問題 | 跨維度整合合成 | 維度是思考工具，不是問題清單 |
| 問題深度 | 初始評估後動態決定 | 內部工具 vs 外部產品需要的深度不同 |
| 維度清單 | AI 可自行擴充 | 認知鷹架而非執行規則 |
| 開場 | AI 先腦力激盪再問 | 指導教授角色：先挑戰方向再起草 |
| 品質驗證 | 輕量提示不強制 | 避免阻塞流程，PM 有最終決定權 |
| 發布 | 多平台按需 | 不假設工具，偵測到就加成 |
| 設定檔 | `.prd-writer.yml` 自動建立 | 記住工具設定，不用每次重填 |
| 名字 | TBD | 候選：Specwright、Speqio、Prodraft |
