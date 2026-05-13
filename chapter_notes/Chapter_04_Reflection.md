# Chapter 04 — Reflection（自我反思）

**完成日期**：2026-05-07  
**狀態**：✅ 已完成

## 重點概念

- Reflection 將 LLM 拆成兩個角色：**Generator（生成者）** 和 **Reflector（反思者）**，原書亦稱此為 **Generator-Critic** 或 **Producer-Reviewer** 模型
- 兩個角色可以是同一個 LLM（用不同 prompt 驅動），也可以是獨立 Agent
- 核心動機：LLM 存在**認知偏見（cognitive bias）**——自己產生的輸出往往覺得「夠好了」，難以客觀批判；角色分離強制引入外部視角，降低這種偏見
- 核心價值：角色分離降低偏見，讓系統具備自我驗證與漸進改善的能力

## 兩種架構

### 單輪反思（One-shot Reflection）
```
輸入 → Generator → 草稿 → Reflector → 批評報告
```
適合「生成 + 驗證」場景，不需迭代，只確認品質。

### 迭代反思迴圈（Iterative Reflection Loop）
```
輸入 → Generator → 草稿
              ↑         ↓
          修正後      Reflector → 批評
              ↑         ↓
          (重複直到滿意或達到最大次數)
```
每輪把批評回饋給 Generator，讓它在下一輪改進。

## 停止條件

| 方式 | 說明 |
|------|------|
| 最大迭代次數 | 跑到 N 輪就停，不管品質 |
| 品質信號 | Reflector 判斷「已夠好」時輸出特定關鍵字（如 `CODE_IS_PERFECT`） |
| 兩者並用 | 最常見，達到任一條件就停 |

## 三個範例對照

| 範例 | 框架 | 架構 | 特點 |
|------|------|------|------|
| ADK | Google ADK | 單輪，SequentialAgent | Generator → FactChecker，結構化輸出（ACCURATE / INACCURATE） |
| Iterative Loop | 純 Python + LangChain | 迭代迴圈，最多 3 輪 | message history 帶入下一輪，有提前終止機制 |
| LangChain | LangChain | 單輪，Pipeline 串接 | `RunnablePassthrough.assign` 逐步累積 context |

## 設計原則

- **結構化輸出**：Agent 間溝通盡量用結構化格式（如含 `status`/`reasoning` 的 dict），方便下游程式條件判斷，不需再解析自然語言
- **message history 的必要性**：迭代迴圈中，前一輪的草稿與批評必須帶入下一輪，LLM 才能有針對性地修正，而非重新生成不相關的結果

## 理解測驗 Q&A

### Q1（選擇題）
**題目**：在迭代反思迴圈中，若沒有設定任何停止條件，最可能出現什麼問題？  
A. 程式直接報錯  B. 無限迴圈，資源耗盡  C. LLM 自動停止  D. 輸出品質下降至空白  

**答案**：B  
**評分**：✅ 正確  
**回饋**：無停止條件 → 無限迭代 → 資源耗盡或 API 超時，同時造成費用暴增。

---

### Q2（簡答題）
**題目**：在 Iterative Loop 範例中，message history 扮演什麼角色？為什麼不能每輪都重新開始空白對話？  

**答案**：必須把前一輪的產物給 LLM 反思形成正循環  
**評分**：✅ 正確（核心到位）  
**回饋**：更精確的說法——message history 讓 Generator 在下一輪能看到「上一版程式碼 + 對應批評」，才能有針對性地修正。若每輪重啟，LLM 不知道改什麼，只會重新生成一份無關的結果。

---

### Q3（情境題）
**題目**：建一個「履歷審查系統」，Generator 產生改善建議，Reflector 評估建議是否夠具體可行。  
a. 單輪反思還是迭代迴圈？  
b. 停止條件怎麼設計？  

**答案**：  
a. 迭代迴圈，品質比較好  
b. 最大交互限制 + LLM 給夠好的信號  

**評分**：✅ 完全正確  
**回饋**：選迭代迴圈理由充分；兩種停止條件並用正是最佳實踐——最大次數保效率，品質信號保品質。

---

### Q4（選擇題）
**題目**：FactChecker 輸出格式被要求為含 `"status"` 和 `"reasoning"` 的 dict，主要好處是？  
A. 減少 token  B. 方便下游條件判斷  C. 跑得更快  D. 降低幻覺  

**答案**：B  
**評分**：✅ 正確  
**回饋**：結構化輸出的核心價值是可程式化，`if status == "INACCURATE": trigger_fix()`，不需再解析自然語言。Agent 間溝通盡量用結構化格式是重要設計原則。

## 相關 Notebooks

- `Chapter_04_Reflection_(ADK).ipynb`
- `Chapter_04_Reflection_(Iterative_Loop).ipynb`
- `Chapter_04_Reflection_(LangChain).ipynb`
