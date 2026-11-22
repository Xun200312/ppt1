# BIWI 資料集 (BIWI 3D Audiovisual Corpus of Affective Communication)

## 1. 概述與定義
**BIWI** (Fanelli et al., IEEE TMM 2010) 是 3D 臉部動畫領域與 VOCASET 齊名的兩大經典黃金標準資料集之一。該資料集使用高精度的 3D 結構光動態掃描系統，捕捉了多位受試者在說話過程中的臉部 3D 網格形變與同步音訊。

## 2. 核心技術細節 / 資料結構
- **網格拓撲與維度**:
  - BIWI 擁有原生的稠密 3D 網格拓撲，每個影格包含高達 **23,370 個頂點** ($V = 23370$)，幾何細節顯著多於 FLAME 拓撲 ($5023$ 頂點)。
- **規模與發音人**:
  - 包含 14 位受試者（6 男 8 女），朗讀各類英語短句與情感語料，影格率為 25 FPS。
- **標準分割 (Standard Split)**:
  - 依照 FaceFormer (CVPR 2022) 制定的業界標準分割協議：
    - **BIWI-Train**: 訓練集。
    - **BIWI-Val**: 驗證集。
    - **BIWI-Test-A**: 測試集 A（見過的說話人說未見過的句子，測試同一身分的泛化性）。
    - **BIWI-Test-B**: 測試集 B（未見過的全新說話人，測試跨身分的泛化性）。

## 3. 在 3D 臉部動畫中的定位與重要性
- **高密度幾何細節 Benchmark**: 專門用於檢驗模型在超高解析度 Mesh 上的生成能力（例如邊緣平滑度、細微肌肉紋理）。
- **SOTA 競賽主戰場**: 在 BIWI-Test-A 上的 LVE (Lip Vertex Error) 與 FDD (Facial Dynamics Deviation) 是衡量模型嘴型精準度最關鍵的量化指標。

## 4. BIWI-Test-A 歷史指標演進榜
| 模型 | 年份 | 架構類別 | LVE ↓ ($\times 10^{-4}$ mm) | FDD ↓ ($\times 10^{-5}$ mm) | 推論時間 |
| :--- | :---: | :--- | :---: | :---: | :---: |
| VOCA | 2019 | CNN | 6.5563 | 8.1816 | 0.1600s |
| MeshTalk | 2021 | Cross-modality | 5.9181 | 5.1025 | 0.2149s |
| FaceFormer | 2022 | Autoregressive Transformer | 5.3027 | 4.6376 | 0.2974s |
| CodeTalker | 2023 | VQ-VAE + Codebook | 4.7923 | 4.1035 | 1.5780s |
| FaceXHuBERT | 2023 | HuBERT + Transformer | 4.5673 | 4.9675 | 0.5219s |
| FaceDiffuser | 2023 | Diffusion Model | 4.2986 | 3.9098 | 0.5500s |
| SelfTalk | 2023 | Self-supervised | 4.2485 | 3.5760 | 0.1240s |
| **RefFace** | **2025** | **Landmark-guided Diffusion** | **3.8466** | **2.9857** | **0.3640s** |

## 5. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | RefFace | 在 BIWI-Test-A 創下當前最佳的 LVE 與 FDD | [[2025_RefFace]] |
| 2023 | FaceDiffuser | 作為擴散模型評測基準 | [[FaceDiffuser]] |
| 2023 | CodeTalker | 作為離散動作先驗評測基準 | [[CodeTalker]] |
| 2022 | FaceFormer | 確立近代業界通用的 BIWI 訓練與測試分割規範 | [[FaceFormer]] |
