# DeepSpeech (百度語音識別聲學特徵)

## 1. 概述與定義
**DeepSpeech** (Hannun et al., 2014) 是百度提出的一種端到端自動語音識別 (ASR) 架構。在 3D 臉部動畫與談話頭像生成領域，DeepSpeech 網路前幾層抽取的聲學特徵（Acoustic Feature Frames）曾被廣泛用作音訊內容特徵。

## 2. 核心技術細節 / 資料結構
- **特徵維度**:
  - 原生提取的音訊內容特徵為 $a_t \in \mathbb{R}^{16 \times 29}$，為捕捉時序上下文，通常前後各延伸 $l$ 個影格，形成 $a^{t-l:t+l}$。
- **與自監督模型 (HuBERT / Wav2Vec 2.0) 的對比**:
  - **優點**: 特徵維度較為小巧，推論計算量小，能穩定提取音素對齊邊界。
  - **局限**: 相較於近年參數量巨大的自監督模型（如 [[HuBERT]] 與 [[Wav2Vec2]]），DeepSpeech 在跨語種泛化、語音語調起伏與微細唇齒音（如爆破音閉唇）的細節捕捉上略遜一籌。

## 3. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | ESGaussianFace | 採用 DeepSpeech 提取語音內容特徵，經 ESAM 空間注意力引導至唇部高斯點 | [[2026_ESGaussianFace]] |
| 2021 | AD-NeRF | 採用 DeepSpeech 特徵驅動隱式神經輻射場 | [[AD-NeRF]] |
