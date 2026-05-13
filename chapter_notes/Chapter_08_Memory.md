# Chapter 08 — Memory（記憶）

**完成日期**：2026-05-07  
**狀態**：✅ 已完成  
**測驗方式**：費曼技巧 + 追問

## 重點概念

- Memory 解決 LLM 無狀態的根本限制，讓 Agent 能跨回合、跨 session 保留資訊
- 記憶不是越多越好：需考量 token 成本、隱私風險、遺忘機制

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

## 相關 Notebooks

- `Chapter_08_Memory_(ADK_MemoryService_InMemory).ipynb`
- `Chapter_08_Memory_(ADK_SessionService).ipynb`
- `Chapter_08_Memory_(ADK_Explicit_State_Update).ipynb`
- `Chapter_08_Memory_(ADK_LlmAgent_output_key).ipynb`
- `Chapter_08_Memory_(LangChain_LangGraph).ipynb`
