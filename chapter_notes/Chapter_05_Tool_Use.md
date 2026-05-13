# Chapter 05 — Tool Use（工具使用）

**完成日期**：2026-05-07  
**狀態**：✅ 已完成

## 重點概念

- Tool Use 讓 LLM 從「只能說話」進化到「能採取行動」
- LLM 本身無法直接執行工具，而是由執行環境（AgentExecutor / Runner）負責呼叫並回傳結果；LLM 呼叫工具時輸出的是 **JSON 格式**的呼叫指令，由執行環境解析後實際執行
- 工具呼叫循環可重複多次（多輪），直到 LLM 判斷資訊足夠才輸出最終答案

## Function Calling vs Tool Calling

| 概念 | 說明 |
|------|------|
| **Function Calling** | 較狹義：LLM 呼叫預先定義的特定函式 |
| **Tool Calling** | 較廣義：工具可以是 API、資料庫查詢、程式碼執行，甚至**另一個 Agent** |

原書明確區分這兩個術語。Function Calling 是 Tool Calling 的子集；現代 Agentic 系統中，工具的概念已大幅擴展——其他 Agent 也可以作為工具被呼叫，形成巢狀的 Agent 架構。

## 工具呼叫流程

```
使用者輸入
    ↓
LLM 決策：「我需要工具 X，參數是 Y」
    ↓
執行環境呼叫工具
    ↓
工具回傳結果
    ↓
LLM 根據結果產生最終回答（或繼續呼叫下一個工具）
```

## 工具的三個要素

| 要素 | 說明 |
|------|------|
| **名稱** | 工具的識別符 |
| **描述（docstring）** | LLM 靠這個決定何時、是否呼叫工具 ← 最關鍵 |
| **參數 schema** | 輸入格式與型別 |

## 兩種工具類型

### 外部資訊工具（LangChain 範例）
```python
@tool
def search_information(query: str) -> str:
    """Provides factual information on a given topic..."""
```
- `AgentExecutor` 負責執行工具並把結果回傳給 LLM

### 程式碼執行工具（ADK 範例）
```python
code_agent = LlmAgent(code_executor=BuiltInCodeExecutor(), ...)
```
- LLM 自行撰寫 Python 程式碼，交給沙盒執行
- 強大但需沙盒隔離確保安全

## ReAct 框架

```
Thought：我需要知道法國的首都
Action：呼叫 search_information("capital of france")
Observation：The capital of France is Paris.
Thought：已有答案
Final Answer：法國的首都是巴黎
```

## 安全考量

| 工具類型 | 風險 | 緩解方式 |
|----------|------|----------|
| 程式碼執行 | 執行惡意程式碼 | 沙盒（BuiltInCodeExecutor）隔離 |
| 外部 API | 存取敏感資料、費用 | 權限控管、rate limit |
| 資料庫操作 | 刪除/修改資料 | 唯讀模式、Human-in-the-loop |

## 理解測驗 Q&A

### Q1（選擇題）
**題目**：LLM 決定「要呼叫哪個工具」時，最主要的依據是什麼？  
A. 工具函式的程式碼內容  B. 工具的名稱和描述  C. 回傳值型別  D. 排列順序  

**答案**：B  
**評分**：✅ 正確  
**回饋**：LLM 完全看不到工具程式碼，只靠名稱和 docstring 決策。描述不清楚 = 工具在錯誤時機被呼叫或被忽略。

---

### Q2（簡答題）
**題目**：請舉一個情境說明為什麼需要多輪工具呼叫？  

**答案**：有些問題可能需要多步驟與調用多工具才能完成  
**評分**：✅ 正確（補充具體情境）  
**回饋**：具體例子——「訂明天飛往東京的最便宜機票」需要：搜尋航班 → 比價 → 訂票，三輪工具呼叫缺一不可。

---

### Q3（情境題）
**題目**：設計「查詢天氣並推薦穿著」的工具，docstring 和參數格式應如何設計？  

**答案**：根據天氣情況推薦穿著；輸入參數要有天氣，回傳值是穿著建議  
**評分**：⚠️ 方向正確，輸入設計有盲點  
**回饋**：可拆成兩個工具——`get_weather(city: str)` 和 `recommend_outfit(weather: str)`。關鍵盲點：第一個工具的輸入是**城市名稱**，不是天氣資料（要先查天氣才有天氣資料）。docstring 需說明觸發時機，如「當使用者詢問特定城市的當前天氣時使用」。

---

### Q4（選擇題）
**題目**：讓 Agent 執行程式碼時，最關鍵的安全機制是？  
A. LLM 先解釋用途  B. 沙盒隔離  C. 限制 10 行以內  D. 密碼確認  

**答案**：B  
**評分**：✅ 正確  
**回饋**：沙盒確保即使 LLM 生成危險程式碼，也無法存取主機系統、檔案或網路。ADK 的 `BuiltInCodeExecutor` 扮演此角色。

## 相關 Notebooks

- `Chapter_05_Tool_Use_(LangChain).ipynb`
- `Chapter_05_Tool_Use_(Executing_Code).ipynb`
- `Chapter_05_Tool_Use_(CrewAI).ipynb`
- `Chapter_05_Tool_Use_(Google_Search).ipynb`
- `Chapter_05_Tool_Use_(Vertex_AI_Search).ipynb`
