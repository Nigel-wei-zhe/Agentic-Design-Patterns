# Chapter 03 — Parallelization（平行化）

**完成日期**：2026-05-06  
**狀態**：✅ 已完成

## 重點概念

- Parallelization 讓多個互相獨立的 LLM 任務同時執行，而非依序等待
- **適用條件**：子任務之間沒有資料依賴關係
- **兩種主要架構**：
  - Fan-out / Fan-in：同一輸入分發給多個 Agent，最後由合併者整合
  - Map-Reduce 風格：同一輸入同時產生多種輸出再合成
- **關鍵元件**（Google ADK）：
  - `ParallelAgent`：同時執行多個子 Agent
  - `SequentialAgent`：協調「先平行、後合併」的外層流程
  - `output_key`：子 Agent 將結果寫入共享 Session State
- **關鍵元件**（LangChain）：
  - `RunnableParallel`：並行執行多條 Chain
  - `RunnablePassthrough`：將原始輸入原封不動傳入下一步

## 架構示意圖（ADK 範例）

```
SequentialAgent（ResearchAndSynthesisPipeline）
├── ParallelAgent（同時執行）
│   ├── RenewableEnergyResearcher → state["renewable_energy_result"]
│   ├── EVResearcher              → state["ev_technology_result"]
│   └── CarbonCaptureResearcher  → state["carbon_capture_result"]
└── SynthesisAgent（讀取 3 個 state key，產出報告）
```

## 理解測驗 Q&A

### Q1（選擇題）
**題目**：下列哪種情況最不適合使用 Parallelization？  
A. 同時向 3 個 Agent 詢問同一份文件的不同面向  
B. 先請 Agent A 總結文章，再請 Agent B 根據該總結提問  
C. 同時對 5 個候選答案各自進行評分  
D. 同時產生摘要、問題、關鍵詞三份輸出  

**答案**：B  
**評分**：✅ 正確  
**回饋**：B 存在資料依賴（Agent B 需要 Agent A 的輸出），必須循序執行，不適合平行化。

---

### Q2（簡答題）
**題目**：在 Google ADK 的 ParallelAgent 範例中，3 個研究型子 Agent 如何把各自的結果傳遞給最後的 SynthesisAgent？  

**答案**：「Fan-in」  
**評分**：⚠️ 方向正確，機制說明不完整  
**回饋**：完整機制是——每個子 Agent 設定 `output_key` 參數，執行後將結果寫入共享的 **Session State**（dict）；SynthesisAgent 的 prompt 用 `{key_name}` 模板語法讀取。「Fan-in」描述的是整體方向，但關鍵技術細節是 `output_key + Session State`。

---

### Q3（情境題）
**題目**：建一個「多語言翻譯系統」，把英文同時翻成繁體中文、日文、西班牙文。  
a. 適不適合平行化？為什麼？  
b. Fan-out 和 Fan-in 各對應哪個步驟？  

**答案**：  
a. 適合  
b. Fan-out = 英文傳給三種語言的 Agent；Fan-in = 把結果彙整給使用者  

**評分**：✅ 完全正確  
**回饋**：三個翻譯任務互相獨立，完全符合平行化的適用條件，結構分析也正確。

---

### Q4（進階選擇題）
**題目**：若兩個平行 Agent 使用了相同的 `output_key`，最可能發生什麼？  
A. 程式直接報錯  
B. 兩個結果都被保留，以 list 形式儲存  
C. 後完成的 Agent 覆蓋先完成的 Agent 的結果  
D. 兩個 Agent 會自動協商決定誰的結果優先  

**答案**：C  
**評分**：✅ 正確  
**回饋**：Session State 是 key-value dict，相同 key 直接覆寫，設計時要確保各子 Agent 使用不同的 `output_key`。

## 相關 Notebooks

- `Chapter_03_Parallelization_(Google_ADK).ipynb`
- `Chapter_03_Parallelization_(LangChain).ipynb`
