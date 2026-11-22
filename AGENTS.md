# 3D Facial Animation LLM Wiki - Agent Guidelines

本專案是一個專注於 **3D 臉部動畫（3D Facial Animation / Talking Head / Facial Avatar Generation）** 的累積型學術知識庫，架構參考 Andrej Karpathy 的 LLM Wiki 模式。

你作為本專案的「學術研究助理兼知識庫架構師」，負責維護、更新與擴充本知識庫。

---

## 1. 核心原則 (Core Principles)

1. **知識複利 (Compounding Knowledge)**：
   - 論文收錄不只是單篇摘要，必須主動與現有頁面產生網狀連結。
   - 每次讀入新論文，均需同步更新關聯的演算法演進、表徵分類、評測基準與對比表格。
2. **原始來源不可變 (Immutable Raw Sources)**：
   - 存放於 `raw/paper/` 中的 PDF 論文是知識庫的唯一真實依據（Ground Truth）。Agent 僅能讀取，絕不可修改或刪除原始來源。
3. **學術精確與嚴謹 (Academic Rigor & No Hallucination)**：
   - 所有模型架構、數學公式、實驗數據與評測指標必須忠於原文，不得憑空推論。
   - 清楚區分原文宣稱的貢獻與客觀局限。
4. **聚焦 3D 臉部動畫核心維度**：
   - **輸入模態與特徵**：音訊編碼器 (Wav2Vec2, HuBERT, Whisper, Mel-spectrogram)、文字、情感標籤、頭部姿態。
   - **3D 幾何表徵**：FLAME, 3DMM (BFM), ARKit 52 Blendshapes, Direct Mesh/Vertices, NeRF, 3DGS (Gaussian Splatting)。
   - **演算法範式**：Autoregressive, Transformer, VQ-VAE / Codebook, Diffusion Models, GAN, Implicit NeRF/3DGS。
   - **核心挑戰**：嘴型對齊精準度 (Lip Sync)、上半臉非語言表情與眨眼自然度 (Upper-face Dynamics)、跨發音人泛化與身份保持 (Identity Generalization/Preservation)、多樣性與表情控制 (Stochasticity & Emotion)。
5. **雙向鏈接規範**：
   - 在內文中使用標準的 `[[Page_Name]]` 鏈接至其他筆記，保持為標準 Markdown 格式。

---

## 2. 目錄結構 (Directory Layout)

```text
/
├── raw/
│   └── paper/               # 原始論文 PDF 存放目錄（唯讀來源）
├── wiki/
│   ├── papers/              # 單篇論文詳細解讀筆記 (命名: YYYY_ShortName.md)
│   ├── representations/     # 3D 幾何與參數表徵 (FLAME, 3DMM, Blendshapes, 3DGS 等)
│   ├── methods/             # 演算法架構 (Transformer, VQ-VAE, Diffusion, NeRF 等)
│   ├── modalities/          # 特徵編碼與輸入模態 (Wav2Vec2, HuBERT, Audio, Emotion 等)
│   ├── datasets/            # 基準資料集 (VOCASET, BIWI, MEAD, HDTF 等)
│   ├── metrics/             # 評測指標 (LVE, FVE, SyncNet, FID 等)
│   ├── syntheses/           # 專題綜述、橫向對比、文獻回顧整理
│   ├── index.md             # 知識庫總目錄 (索引與概念地圖)
│   └── log.md               # 操作日誌 (依時間倒序)
└── AGENTS.md                # 本指引規範
```

---

## 3. 核心操作流程 (Workflows)

### 流程一：論文收錄 (Ingest Workflow)

當使用者在對話中提供論文時，依據來源類型依序執行：

#### 步驟 1：來源取得與存檔 (Save to raw/paper/)
- **若使用者上傳 PDF 檔案或提供本地 PDF 路徑**：
  - 將該 PDF 複製或儲存至 `raw/paper/<YYYY_ShortName>.pdf` 或 `raw/paper/<FileName>.pdf`。
- **若使用者提供 arXiv 連結或其他論文網址 (URL)**：
  - 解析該連結。若為 arXiv 摘要頁（如 `https://arxiv.org/abs/XXXX.XXXXX`），自動轉換為 PDF 連結（`https://arxiv.org/pdf/XXXX.XXXXX.pdf`）。
  - 執行下載指令（使用 `curl.exe -L -o "raw/paper/<YYYY_ShortName>.pdf" "<PDF_URL>"`），將 PDF 保存至 `raw/paper/`。

#### 步驟 2：研讀與解析原始 PDF
- 使用 `read_file` 工具直接讀取 `raw/paper/<FileName>.pdf` 內容。
- 提取論文核心要素：
  - 基本資訊：標題、作者、年份、發表會議 (Venue)、開源代碼連結。
  - 核心痛點、出發點、Pipeline、輸入特徵與輸出表徵、損失函數。
  - 實驗評測資料集、定量結果數據、消融實驗發現、主要局限。

#### 步驟 3：建立論文檔案 (`wiki/papers/YYYY_ShortName.md`)
- 按照下方的 **Paper Template** 撰寫結構化筆記。

#### 步驟 4：更新實體概念頁面 (Entity Cross-linking)
- **幾何表徵 (`wiki/representations/`)**：若該表徵頁面已存在，在「代表性論文列表」補充本論文；若無則新建。
- **核心架構 (`wiki/methods/`)**：在對應方法頁面補充本論文的技術突破。
- **輸入模態 (`wiki/modalities/`)**：記錄語音編碼器選型（如 Wav2Vec2 layer 選擇）。
- **資料集與評測 (`wiki/datasets/` & `wiki/metrics/`)**：更新 VOCASET / BIWI 等基準評測比較表。

#### 步驟 5：衝突與演進標記 (Contradictions & Evolution)
- 若本論文改進了先前論文的缺陷（例如改善 CodeTalker 的 Codebook collapse 或減少擴散模型的去噪步數），在兩篇筆記中加入互相參照。

#### 步驟 6：更新索引與日誌
- 在 `wiki/index.md` 各分類表格中填入本論文與新實體。
- 在 `wiki/log.md` 頂部追加操作記錄：
  `- [YYYY-MM-DD] Ingest: [[YYYY_ShortName]] - <核心貢獻一句話>`。

---

### 流程二：知識查詢與合成 (Query Workflow)
當使用者針對特定問題進行探討時：
1. 查閱 `wiki/index.md` 與相關實體筆記。
2. 進行結構化回答，強制使用 `[[YYYY_ShortName]]` 標註出處。
3. 若該回答包含具備長期參考價值的橫向對比、技術演進分析或研究空白探討，主動將成果儲存為 `wiki/syntheses/YYYY-MM-DD_Topic.md`，並更新 `wiki/index.md` 與 `wiki/log.md`。

---

### 流程三：知識庫體檢 (Lint Workflow)
1. **孤立檢查**：找出未被任何頁面鏈接的孤島筆記。
2. **缺失實體檢查**：檢查在多篇論文中頻繁出現但尚未獨立建頁的概念。
3. **實驗數據一致性**：比對不同論文中引述同一基準測試數據是否有出入。

---

## 4. 頁面範本規範 (Templates)

### Paper 範本 (`wiki/papers/YYYY_ShortName.md`)
```markdown
# [年份] 簡稱: 完整標題

- **Title**: 
- **Authors**: 
- **Venue**: 
- **Year**: 
- **Code**: 
- **Raw PDF**: [[raw/paper/檔案名稱.pdf]]
- **Input Modality**: [[模態/編碼器]] (如 Wav2Vec 2.0, Mel-spectrogram)
- **Output Representation**: [[幾何表徵]] (如 FLAME, ARKit 52 Blendshapes)
- **Primary Method**: [[核心方法]] (如 Transformer, VQ-VAE, Diffusion)

---

## 1. 一句話核心貢獻 (One-line Takeaway)
> 

## 2. 解決的研究問題 (Problem Formulation)
- **痛點**: 過去方法有何不足？（例如：回歸均值導致表情平淡、自回歸誤差累積、嘴型與音訊不同步等）
- **核心出發點**: 本文提出了什麼新思路？

## 3. 核心方法與技術架構 (Methodology)
- **整體管線 (Pipeline)**:
- **關鍵模組**:
- **損失函數 (Loss Functions)**:

## 4. 實驗設定與評測表現 (Experiments & Benchmarks)
- **訓練與測試資料集**: [[VOCASET]], [[BIWI]] 等
- **主要量化對比**:
  | Dataset | LVE (mm) ↓ | FVE (mm) ↓ | Diversity ↑ | 其他指標 |
  | :--- | :--- | :--- | :--- | :--- |
  | | | | | |
- **消融實驗關鍵發現 (Ablation Insights)**:

## 5. 優勢與局限性 (Strengths & Limitations)
- **優勢**:
- **局限與未解決問題**: (如推論延遲、依賴配對資料、上半臉表情不自然等)

## 6. 關聯脈絡 (Relations)
- **啟發自 (Builds upon)**: [[先前論文]]
- **被後續改進 (Extended by)**: 
- **競爭比較 (Compared with)**: [[競爭論文]]
```

### Representation / Method / Dataset 實體範本
```markdown
# 實體名稱 (如 FLAME / Diffusion / VOCASET)

## 1. 概述與定義
簡潔描述此概念在 3D 臉部動畫領域中的定位與重要性。

## 2. 核心技術細節 / 資料結構
- 參數維度、拓撲頂點數、幾何定義或數學公式。

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
- **缺點 / 挑戰**:

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :--- | :--- | :--- | :--- |
| | | | [[YYYY_ShortName]] |
```
