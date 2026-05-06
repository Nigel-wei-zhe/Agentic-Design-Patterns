# Agentic Design Patterns — 學習專案

## 專案說明
閱讀《Agentic Design Patterns》(Antonio Gulli) 並實作各章節的 Jupyter notebooks。

## 學習流程
每一小節分三個階段：
1. **導讀**：重點概念講解（繁體中文，關鍵術語附英文原文）
2. **出題**：3–5 題理解測驗（選擇題、簡答題、情境題混合）
3. **回饋**：根據回答確認理解，補充說明後標記進度

章節確認完成後：
- 更新 `READING_PLAN.md` 進度
- 在 `chapter_notes/` 建立該章節的 `.md` 筆記，記錄重點概念、完整 Q&A 與回饋

## 偏好設定
- 語言：繁體中文為主，英文術語保留原文
- 進度追蹤：更新 `READING_PLAN.md` 的 `[ ]` → `[x]`
- Notebooks 位置：`chapter_notebooks/`
- 章節筆記位置：`chapter_notes/`（命名格式：`Chapter_NN_Topic.md`）
- 書籍 PDF：`Agentic_Design_Patterns_Complete.pdf`

## 專案結構
```
Agentic-Design-Patterns/
├── Agentic_Design_Patterns_Complete.pdf   # 書籍原文
├── READING_PLAN.md                        # 進度追蹤
├── CLAUDE.md                              # 專案說明與偏好設定
├── chapter_notebooks/                     # 各章節程式碼範例
└── chapter_notes/                         # 各章節閱讀筆記（Q&A + 回饋）
    ├── Chapter_01_Prompt_Chaining.md
    ├── Chapter_02_Routing.md
    ├── Chapter_03_Parallelization.md
    └── ...
```
