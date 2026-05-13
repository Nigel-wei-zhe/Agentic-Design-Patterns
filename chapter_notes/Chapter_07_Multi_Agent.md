# Chapter 07 — Multi-Agent（多代理人）

**完成日期**：2026-05-07  
**狀態**：✅ 已完成  
**測驗方式**：費曼技巧 + 設計題追問

## 重點概念

- Multi-Agent 將複雜任務分配給多個專門化 Agent 協同完成，核心是**分工 + 協作**
- 不是全新概念，而是前面 Pattern 的 Agent 化組合：
  - Ch01 Prompt Chaining → Sequential
  - Ch02 Routing → Coordinator
  - Ch03 Parallelization → Parallel
  - Ch04 Reflection Loop → Loop

## 六種協作形式

原書列出比四種架構更完整的協作類型：

| 協作形式 | 說明 |
|----------|------|
| Sequential | 子 Agent 依序執行，有資料依賴 |
| Parallel | 子 Agent 同時執行，互相獨立 |
| Coordinator | 主 Agent 根據語意動態路由 |
| Loop | 反覆執行直到條件滿足 |
| **Debate & Consensus** | 多個 Agent 各持不同立場辯論，最後形成共識；適合需要多角度審視的決策 |
| **Expert Teams** | 不同專業領域的 Agent 組成團隊，各自負責自己的專長領域再協作整合 |

## 系統穩健性

Multi-Agent 架構的重要優點之一：**單一 Agent 失敗不等於整體失敗**。分散式架構讓系統在部分元件失效時仍能繼續運作，提升整體穩健性（robustness）。

## 四種編排架構（ADK 實作）

### 1. Coordinator（協調者）
- 主 LlmAgent 根據輸入語意動態決定派給哪個子 Agent
- 靠 LLM 理解與推理做路由決策

### 2. Sequential（循序）
- 子 Agent 依序執行，透過 `output_key` + Session State 傳遞資料
- 適合有明確資料依賴的流程

### 3. Parallel（平行）
- 子 Agent 同時執行，各存入不同 `output_key`
- 適合互相獨立的子任務

### 4. Loop（迴圈）
- 子 Agent 反覆執行，直到某 Agent 發出 `escalate=True` 或達到 `max_iterations`

## 架構選擇指南

| 情境 | 建議架構 |
|------|----------|
| 不同請求要不同處理方式 | Coordinator |
| 步驟間有資料依賴 | Sequential |
| 步驟間互相獨立 | Parallel |
| 需反覆執行直到條件滿足 | Loop |

## 費曼技巧 Q&A

### 費曼提問：Coordinator 和 Routing（Ch02）的差別？

**回答**：兩者差異主要在前者靠 LLM 決定，後者是架構者自行定義邏輯。  
**評分**：✅ 核心差異正確

### 追問：什麼情況選 Coordinator，什麼情況選 Routing？代價各是什麼？

**回答**：輸入格式可控已知選 Routing，輸入多元多樣化選 Coordinator（LLM）。  
**評分**：✅ 正確  
**補充回饋**：
- Routing 的代價：維護成本高，每新增輸入類型就要改規則，彈性差
- Coordinator 的代價：多一次 LLM 呼叫（延遲 + 費用），偶爾路由錯誤，較難除錯

> 輸入可預測 → Routing（快、穩、便宜）  
> 輸入多變複雜 → Coordinator（彈性高，但有成本）

## 相關 Notebooks

- `Chapter_07_Multi_Agent_(ADK_Gemini_Coordinator).ipynb`
- `Chapter_07_Multi_Agent_(ADK_Gemini_Sequential).ipynb`
- `Chapter_07_Multi_Agent_(ADK_Gemini_Parallel).ipynb`
- `Chapter_07_Multi_Agent_(ADK_Gemini_Loop).ipynb`
- `Chapter_07_Multi_Agent_(ADK_Gemini_AgentTool).ipynb`
- `Chapter_07_Multi_Agent_(CrewAI_Gemini).ipynb`
