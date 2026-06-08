# PRD Template

## 使用說明

此為基礎結構。AI 在 Clarify 過程中若偵測到需要額外區塊，應主動建議加入。  
常見的額外區塊：Security Considerations、Analytics Requirements、Compliance、Multi-language、Notification Design 等。

**mandatory** = 必填；**optional** = 視功能性質決定。

---

# [功能名稱] — PRD

**Ticket：** `<TICKET_ID>` — [Ticket 標題]（若有）  
**建立日期：** [DATE]  
**狀態：** Draft  
**作者：** [PM 名稱]  
**語言：** [zh-TW / en]

---

## Story Background *(mandatory)*

### Problem Statement
[描述現有的痛點或機會。具體說明誰受影響、如何受影響。]

### Business Goal
[從業務角度，成功的樣貌是什麼？]

### Current State
[使用者目前如何處理這個問題？有什麼 workaround？]

---

## User Scenarios & Testing *(mandatory)*

> 使用者旅程應按重要性排序。每個 Story 必須能獨立測試。

### User Story 1 — [Brief Title] (Priority: P1) 🎯 MVP

[白話文描述這個使用者旅程]

**Why this priority：** [說明價值與優先理由]

**Independent Test：** [如何單獨驗證這個 story]

**Acceptance Scenarios：**
1. **Given** [初始狀態], **When** [操作], **Then** [預期結果]
2. **Given** [初始狀態], **When** [操作], **Then** [預期結果]

---

### User Story 2 — [Brief Title] (Priority: P2)

[同上格式]

---

### Edge Cases

- [邊界條件] 時會怎樣？
- [錯誤情境] 時系統如何回應？
- [意外情況] 時使用者看到什麼？

---

## Scope *(mandatory)*

### In Scope
- [包含的功能 / 行為]

### Out of Scope
- [明確排除的功能 / 行為]

### Phase Boundaries
- **Phase 1 (MVP)：** [最小可行範圍]
- **Phase 2：** [下一輪迭代範圍]

---

## Requirements *(mandatory)*

> 專注於 WHAT，不是 HOW。沒有技術術語。每個需求必須可測試。

### Functional Requirements

- **FR-001：** System MUST [從使用者角度描述的具體能力]
- **FR-002：** Users MUST be able to [關鍵互動]
- **FR-003：** System MUST [具體行為]

*不清楚的需求使用標記：*
- **FR-004：** System MUST [能力] `[NEEDS CLARIFICATION: 具體問題]`

### Key Entities *(如果功能涉及資料)*

- **[Entity 1]：** [代表什麼、關鍵屬性（不含實作細節）]
- **[Entity 2]：** [代表什麼、與其他 entity 的關係]

---

## Experience Expectations *(optional)*

> 定義期望，不是技術方案。「必須感覺即時」而非「使用 WebSocket」。

- **Loading experience：** [例如「必須感覺即時」/ 「可以顯示載入指示器」]
- **Responsiveness：** [例如「必須在手機和桌面都能用」]
- **Accessibility：** [例如「必須支援鍵盤導航」]
- **Offline behavior：** [例如「離線時顯示快取內容」]

---

## Success Criteria *(mandatory)*

> 可測量、以使用者為中心、不提技術工具。

- **SC-001：** [使用者指標，例如「使用者能在 2 分鐘內完成任務」]
- **SC-002：** [效能指標，例如「頁面在 3G 下 3 秒內載入」]
- **SC-003：** [業務指標，例如「減少 50% 相關支援票」]

---

## Clarifications *(由 Clarify 流程自動填入)*

### Session [DATE]
- Q: [問題] → A: [答案]
- Q: [問題] → A: [答案]

---

## Assumptions *(optional)*

- [假設 1，例如「現有登入流程維持不變」]
- [假設 2，例如「資料量在 10,000 筆以內」]

---

## 原始解法建議 *(reference only — 僅供背景參考)*

> ⚠️ 此區塊為背景參考，不是需求。實際需求在 User Stories 和 Requirements。

| 來源 | 原始描述 | 處理狀態 |
|------|---------|----------|
| [客戶 / PM / Ticket] | 「[原始建議]」 | ✅ 已轉化為需求 / 📝 僅作參考 / 🔍 [NEEDS RCA] |

---

<!-- 以下為 AI 依功能性質建議加入的額外區塊範例 -->

## Security Considerations *(optional — 涉及敏感資料或權限時建議加入)*

- [安全考量 1]
- [安全考量 2]

## Analytics Requirements *(optional — 需要追蹤使用行為時建議加入)*

- **事件 1：** [觸發條件] → [記錄什麼]
- **事件 2：** [觸發條件] → [記錄什麼]

## Compliance & Legal *(optional — 涉及法規或隱私時建議加入)*

- [合規要求]

---

## AI Generation Guidelines

1. **Make informed guesses：** 用上下文、業界慣例填補空白
2. **Document assumptions：** 合理預設值記錄在 Assumptions 區塊
3. **Limit clarifications：** 最多 3 個 `[NEEDS CLARIFICATION]`，只用於真正影響範圍或體驗的決策
4. **No technical jargon：** 避免框架、API、資料庫、實作模式
5. **解法建議 ≠ 需求：** 具體解法建議先放「原始解法建議」，透過 Clarify 確認性質
6. **主動建議額外區塊：** 若 Clarify 過程發現功能涉及安全、法規、分析等，主動建議加入對應區塊
