# Chapter 06 — Planning（規劃）

**完成日期**：2026-05-07  
**狀態**：✅ 已完成  
**測驗方式**：費曼技巧 + 追問

## 重點概念

- Planning 讓 Agent 在執行任務前先產生結構化計劃，確保方向正確、步驟清楚
- 計劃最大的價值不是「保證方向正確」，而是讓系統**可解釋、可追查**——出錯時能判斷是計劃本身有問題，還是執行偏離了計劃
- Planning 天然是最好的 **Human-in-the-Loop 介入點**，人類可以在執行前審查計劃

## 兩種實作方式

### 內嵌計劃（CrewAI 範例）
Agent 在單一任務中先規劃再執行，計劃是中間產物：
```
Task：先列大綱（Plan） → 根據大綱寫文章（Execute）
```

### 透明計劃（Deep Research API）
模型在回答前自動多輪搜尋與推理，每個中間步驟可被觀察：
- `reasoning`：思考步驟
- `web_search_call`：執行了哪些搜尋
- `code_interpreter_call`：執行了哪些程式碼

## 與其他 Pattern 的組合

| 組合 | 說明 |
|------|------|
| Planning + Tool Use | 計劃哪些工具要呼叫、順序為何 |
| Planning + Reflection | 執行前規劃（防止做錯的事）+ 執行後反思（防止事做得不夠好），兩個時間點各有防線 |
| Planning + Multi-Agent | 主 Agent 規劃，子 Agent 分工執行，最後整合 |

## 費曼技巧 Q&A

### 用戶解釋
> 計劃設計模式是為了在任務開始前確保方向是對的，而先產生一個計劃表並逐步完成。優點是每一步驟都是可檢視，不會是全程黑盒子。計劃設計模式也會搭配其他設計模式一起執行，像是根據計劃預計調用那些工具，計劃完成後透過 Reflection 檢查有沒有遺漏或錯誤，又或者針對不同項目採用不同的 Agent 完成，最後再一起整合。

### 追問 1：如果計劃本身可能是錯的，Planning 還有意義嗎？

**回答**：計劃模式最大的優點是條列式列出計劃，方便人類決定是否執行與判斷方向。就算人類不參與，在任務開始前有個計劃表，就算最後錯誤，才有個依據可以反查。  
**回饋**：核心點抓到了——Planning 的價值是**可解釋性與可追查性**，而非保證正確。這也連接到 Human-in-the-Loop（Ch13），Planning 天然是人類最好的介入點。

### 追問 2：Planning + Reflection 和單獨 Reflection 差在哪？

**回答**：一個是明確定義 workflow，另一個是要自己去執行反思。重點就是建立標準工作流程。  
**回饋**：結構差異抓到了。更精確的說法：Planning 攔截「做了錯誤的事」（事前），Reflection 攔截「事做得不夠好」（事後）。兩者組合在兩個時間點都有防線，防的是不同類型的問題。

## 相關 Notebooks

- `Chapter_06_Planning_(Code_Example).ipynb`（CrewAI）
- `Chapter_06_Planning_(Deep_Research_API).ipynb`
