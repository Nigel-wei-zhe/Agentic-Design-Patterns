# Chapter 08 — Memory（記憶）

**完成日期**：2026-05-07  
**狀態**：✅ 已完成  
**測驗方式**：費曼技巧 + 追問

## 重點概念

- Memory 解決 LLM 無狀態的根本限制，讓 Agent 能跨回合、跨 session 保留資訊
- 記憶不是越多越好：需考量 token 成本、隱私風險、遺忘機制
- **長 context window ≠ 長期記憶**：擴大 context window 只是擴展了短期記憶的容量，資訊依然是暫時性的（session 結束即消失）且成本高昂；這無法取代真正的持久化記憶（SessionService / MemoryService）

## 四種記憶層次

| 層次 | 範圍 | 說明 | 類比 |
|------|------|------|------|
| In-context | 單次呼叫 | 對話歷史放進 prompt | 工作記憶 |
| Session State | 單次 session | `session.state` dict，session 結束即消失 | 短期記憶 |
| Session Service | 跨呼叫持久化 | SQLite / Vertex AI，重啟後仍在 | 長期記憶 |
| Memory Service | 跨 session 語意搜尋 | RAG-based，按內容語意搜尋 | 聯想記憶 |

## ADK 元件對照

```
InMemorySessionService   → 本地開發，重啟即消失
DatabaseSessionService   → SQLite/PostgreSQL，精確 key-value 持久儲存
VertexAiSessionService   → GCP 生產環境

InMemoryMemoryService    → 本地開發
VertexAiRagMemoryService → GCP 生產，向量相似度語意搜尋
```

## State 命名慣例（ADK）

| 前綴 | 範圍 |
|------|------|
| `user:` | 跨 session 持久 |
| `temp:` | 當次 session 暫時 |
| （無前綴） | 當次 session |

## 費曼技巧 Q&A

### 費曼提問：Session State 和 Memory Service 的差別？各自適合什麼場景？

**回答**：Session State 的記憶只活在同一個 session，Memory Service 是長期的記憶庫。前者適合在同一對話中為了完成任務而存在的上下文，後者適合使用者嗜好設定之類的資訊。  
**評分**：✅ 正確，例子直觀

### 追問：DatabaseSessionService 和 MemoryService 都能持久儲存，差別是什麼？

**回答**：Memory Service 支援向量搜尋，模糊語意匹配；Session 靠使用者直接指定（key）存取。  
**評分**：✅ 正確  
**回饋**：
- DatabaseSessionService：精確查找，知道 key 就能取值，像字典
- MemoryService：語意搜尋，用自然語言 query 找「相關」記憶，不需知道確切 key

具體例子：使用者說「我喜歡短句」存進 MemoryService，之後 query `"language preferences"` 就能找到，就算措辭不同也能匹配。這用 Session State 的 key-value 做不到。

## 延伸討論：何時選 Memory Service 而非 Session？

> 此段為補充討論，結合原書架構邏輯整理

**選 Session 的條件：資料有固定結構，存取模式可預測**

Session 是 key-value，存取都要精確指定 key，設計時就必須預先決定資料名稱。
適合的資料：`task_status`、`step`、`user_id` 等有明確結構、之後知道要查哪個 key 的資料。

**選 Memory Service 的條件：資料是自然語言，未來需求無法預測**

Memory Service 的核心優勢是語意搜尋——不需要預先知道 key，只描述「我要找什麼類型的資訊」。
適合的資料：使用者在第 3 次對話說過「我不吃辣」、第 7 次說過「我偶好簡短的回答」。
這些無法預測什麼時候需要，也無法用 key 有效歸類；query 「飲食偉好」就能搨出，不需記得當時存在哪個 key 下。

**一句話總結：**
> 資料有固定結構且存取模式可預測 → Session  
> 資料是自然語言且未來需求無法預測 → Memory Service

這個判斷邏輯與 Ch02 Routing 相似：可預測用規則，不可預測用 LLM。

## 相關 Notebooks

- `Chapter_08_Memory_(ADK_MemoryService_InMemory).ipynb`
- `Chapter_08_Memory_(ADK_SessionService).ipynb`
- `Chapter_08_Memory_(ADK_Explicit_State_Update).ipynb`
- `Chapter_08_Memory_(ADK_LlmAgent_output_key).ipynb`
- `Chapter_08_Memory_(LangChain_LangGraph).ipynb`
