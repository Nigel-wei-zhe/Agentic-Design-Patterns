# Agentic Design Patterns — 學習專案

## 專案說明
閱讀《Agentic Design Patterns》(Antonio Gulli) 並實作各章節的 Jupyter notebooks。

## 學習流程
每一小節分三個階段：
1. **導讀**：重點概念講解（繁體中文，關鍵術語附英文原文）
2. **出題**：3–5 題理解測驗（選擇題、簡答題、情境題混合）
3. **回饋**：根據回答確認理解，補充說明後標記進度

章節確認完成後，依序執行：
1. 更新 `READING_PLAN.md` 進度
2. 在 `chapter_notes/` 建立該章節的 `.md` 筆記，記錄重點概念、完整 Q&A 與回饋
3. commit 並 push 當前分支
4. 對 `main` 發 Pull Request
5. 切回 `main`，為下一章建立新分支，再繼續

## Git 分支規則

- **每章一個獨立分支**，章節完成後發 PR 合併進 `main`，再開下一章的分支
- 分支命名格式：`chapter-NN-topic`（全小寫、用連字號、**不含斜線**）
  - 範例：`chapter-06-planning`、`chapter-07-multi-agent`
- 開始新章節前，先確認目前在 `main` 且已是最新：
  ```bash
  git checkout main && git pull origin main
  git checkout -b chapter-NN-topic
  ```

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
