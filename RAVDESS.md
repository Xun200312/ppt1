# RAVDESS 資料集 (The Ryerson Audio-Visual Database of Emotional Speech and Song)

## 1. 概述與定義
**RAVDESS** (Livingstone & Russo, PLoS ONE 2018) 是一個被廣泛應用於情緒辨識、情感語音合成與視聽人臉生成的經典情感多模態資料集。

資料集由 24 位專業北美演員（12 男 12 女）錄製，涵蓋 8 種基本情感（中性 Neutral、平靜 Calm、高興 Happy、悲傷 Sad、憤怒 Angry、恐懼 Fear、厭惡 Disgust、驚訝 Surprise），且每種情緒均包含正常（Normal）與強烈（Strong）兩種不同強度等級。

## 2. 核心技術細節 / 資料結構
- **規模**: 總計 7,356 段錄影錄音片段（包含語音與清唱兩種模式）。
- **模態**: 高畫質前向 2D 臉部影片（720p/1080p，30 FPS）與無壓縮 48kHz 廣播級音訊。
- **衍生 3D 重構**:
  - [[3D_ETF]]：由 EmoTalk (ICCV 2023) 基於 RAVDESS 重建出的 3D Blendshapes 與 FLAME 拓撲資料集。
  - 在 [[2026_ESGaussianFace]] 與 [[2025_DiffusionTalker]] 中，RAVDESS 均作為檢驗模型多情感合成泛化能力的標準跨資料集測試場。

## 3. 在 3D 臉部動畫中的定位與重要性
- **情緒對比極度標準**: 演員朗讀完全相同的語意文本（如 "Kids are talking by the door"），排除了語言內容對情緒表現的干擾，是驗證語音-情感解耦（Emotion Disentanglement）的理想資料集。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | ESGaussianFace | 與 MEAD 共同作為 8 種情感神經渲染的訓練與評測基準 | [[2026_ESGaussianFace]] |
| 2025 | DiffusionTalker | 訓練個人化情緒對比學習模組（基於其 3D 重構版本 3D-ETF） | [[2025_DiffusionTalker]] |
| 2023 | EmoTalk | 建立 3D-ETF 情感臉部資料集 | [[EmoTalk]] |
