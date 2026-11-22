# VOCASET 資料集

## 1. 概述與定義
**VOCASET** 是由 Cudeiro 等人於 CVPR 2019 隨 **VOCA (Voice Operated Character Animation)** 模型一同發布的 4D 臉部掃描資料集。它是 3D 臉部動畫領域被引用最多、使用最廣泛的經典 Benchmark 之一。

## 2. 核心技術細節 / 資料結構
- **採集設備**: 多相機 3D 動態掃描系統（60 FPS）。
- **受試者數量**: 12 位發音人（6 男 6 女）。
- **幾何表徵**: FLAME 拓撲網格，每個時間步為 $V = 5023$ 個頂點的 3D 座標（共 $5023 \times 3$ 維度）。
- **語音長度**: 每位發音人朗讀 40 句不同長度的英語句子，句子總數 480 條，總音訊時長約 29 分鐘。

## 3. 在 3D 臉部動畫中的定位與重要性
- **標準測試場**: 幾乎所有 3D 語音驅動模型（如 FaceFormer, CodeTalker, SelfTalk, FaceDiffuser）均將其作為核心評測指標 LVE (Lip Vertex Error) 的對比標準。
- **Zero-Shot 測試**: 近期研究（如 [[2025_DiffusionTalker]]）將在其他大規模資料集訓練的模型直接部署至 VOCASET，以檢驗跨領域與跨發音人的零樣本泛化能力。
- **多模型混合訓練**: [[2025_RefFace]] 將 VOCASET 與 MEAD 混合訓練 RefFace-FLAME，使模型同時兼具自然說話發音與高保真情感表達。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | RefFace | 與 MEAD 混合訓練 RefFace-FLAME 模型 | [[2025_RefFace]] |
| 2025 | DiffusionTalker | 作為 Zero-Shot 泛化測試基準 (LVE, FDD) | [[2025_DiffusionTalker]] |
| 2023 | CodeTalker | 主要訓練與測試基準 | [[CodeTalker]] |
| 2022 | FaceFormer | 主要訓練與測試基準 | [[FaceFormer]] |
| 2019 | VOCA | 原始資料集發布者 | [[VOCA]] |
