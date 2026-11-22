# 3D-ETF 資料集 (3D Emotional Talking Face / RAVDESS 3D)

## 1. 概述與定義
**3D-ETF** 是由 EmoTalk (Peng et al., ICCV 2023) 基於知名的情感視聽資料集 **RAVDESS**（The Ryerson Audio-Visual Database of Emotional Speech and Song）重構建構的 3D 情感臉部動畫資料集。

RAVDESS 原始包含 24 名專業演員（12 男 12 女）以 8 種不同情感（中性、平靜、高興、悲傷、憤怒、恐懼、厭惡、驚訝）朗讀特定語句的 2D 影片。3D-ETF 透過單目 3D 重建技術擬合出精確的 3D Blendshapes 或 FLAME 臉部表情序列，成為近年情感 3D 臉部動畫（Emotional 3D Facial Animation）的重要 Benchmark。

## 2. 核心技術細節 / 資料結構
- **情感類別**: 8 類基本情感。
- **說話人數量**: 24 位專業演員。
- **表徵形式**: Blendshapes 權重序列與對應的 FLAME 拓撲參數。
- **採樣率**: 影格率通常為 30 FPS 或 60 FPS，配對高保真音訊。

## 3. 在 3D 臉部動畫中的定位與重要性
- **評測核心**: 成為檢驗模型能否精確表現情緒表情（EVE 指標）與上半臉生動度（FDD 指標）的黃金標準資料集。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | DiffusionTalker | 訓練集與主要定量評測基準（EVE, LVE, FDD, FPS） | [[2025_DiffusionTalker]] |
| 2023 | EmoTalk | 首次構建該 3D 情感資料集並提出 bs2FLAME 拓撲對齊 | [[EmoTalk]] |
