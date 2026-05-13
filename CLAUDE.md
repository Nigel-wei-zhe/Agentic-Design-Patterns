# Agentic Design Patterns — 學習專案

## 專案說明
閱讀《Agentic Design Patterns》 (Antonio Gulli) 並實作各章節的 Jupyter notebooks。

## 導讀準備規範

**每章導讀前，必須先用 Python 讀取原書 PDF 對應章節，再根據原文內容進行導讀。**

```python
import fitz
doc = fitz.open('Agentic_Design_Patterns_Complete.pdf')
# 讀取對應章節頁面
text = ""
for page in doc.pages(start, end):
    text += page.get_text()
```

- 不可僅憑章節標題或 notebook 程式碼推測內容
- 若筆記內容並非來自原書，須在筆記中明確標注「非原書內容」

## 學習流程
每一小節分三個階段：
1. **導讀**：先讀原書對應章節，再整理重點概念（繁體中文，關鍵術語附英文原文）
2. **出題**：3–5 題理解測驗（選擇題、簡答題、情境題混合）
3. **回饋**：根據回答確認理解，補充說明後標記進度

章節確認完成後，依序執行：
1. 更新 `READING_PLAN.md` 進度
2. 在 `chapter_notes/` 建立該章節的 `.md` 筆記，記錄重點概念、完整 Q&A 與回饋
3. commit 並 push 到 `notes` 分支

## Git 分支規則

- 所有閱讀進度統一 commit 到 **`notes`** 分支，`main` 不動
- 每章完成後只需 `git push origin notes`，不需發 PR
- 實際 push 請用 MCP `push_files` 工具（git proxy 有 403 問題）

## 偏好設定
- 語言：繁體中文為主，英文術語保留原文
- 進度追蹤：更新 `READING_PLAN.md` 的 `[ ]` → `[x]`
- Notebooks 位置：`chapter_notebooks/`
- 章節筆記位置：`chapter_notes/`（命名格式：`Chapter_NN_Topic.md`）
- 書籍 PDF：`Agentic_Design_Patterns_Complete.pdf`（透過 pymupdf 讀取）

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
