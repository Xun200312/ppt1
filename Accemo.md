# Acc_emo (Emotion Classification Accuracy, 情感辨識準確率)

## 1. 概述與定義
**$Acc_{emo}$** 是評估情感談話頭像（Emotional Talking Head）生成品質的核心語義感知指標。它衡量的是**獨立的第三方預訓練臉部表情辨識網路（Facial Emotion Classifier）能否正確辨認出生成影片中所傳達的情緒類別**。

單純的幾何指標（如 L2 距離）無法保證生成的面部動作真正具有「憤怒」或「悲傷」的人類心理感知說服力；$Acc_{emo}$ 透過模擬客觀觀察者，直接衡量情感合成的保真度。

## 2. 核心技術細節 / 計算方式
- **評估網路**: 通常採用在大規模動態表情資料集（如 DFEW, FER2013, Aff-Wild2）上預訓練的時序表情分類網路，如 **FAN (Frame Attention Networks)** (Meng et al., ICIP 2019)。
- **計算公式**:
  $$Acc_{emo} = \frac{1}{N} \sum_{i=1}^{N} \mathbb{I}(\text{Classifier}(\hat{V}_i) = y_i^*)$$
  其中 $\hat{V}_i$ 為輸入模型生成的情感影片片段，$y_i^*$ 為目標情緒標籤（如 Happy, Sad, Angry 等）。
- **單位與方向**: 百分比 (%)，**數值越高越好 (↑)**。

## 3. 在 3D 臉部動畫中的定位
- **消融驗證極具辨別力**: 在 [[2026_ESGaussianFace]] 中，未引入情感嵌入 $e$ 時，$Acc_{emo}$ 從 75.94% 暴跌至 27.92%，清楚展現模型是否真正學會了情緒表情特徵。

## 4. 代表性基準表現 (MEAD 與 RAVDESS 基準)
| 模型 | MEAD $Acc_{emo}$ ↑ (%) | RAVDESS $Acc_{emo}$ ↑ (%) | 說明 |
| :--- | :---: | :---: | :--- |
| **ESGaussianFace** | **75.94%** | **71.12%** | 3DGS 結合 ESAM 空間注意力，情緒最鮮明 |
| Style2Talker | 70.27% | 49.54% | 2D 擴散模型 |
| EDTalk | 62.28% | 59.31% | 語音解耦模型 |
| GaussianTalker | 52.62% | 51.31% | 缺乏情感專門設計，常混淆情緒 |
| SyncTalk | 48.25% | 47.68% | NeRF 渲染 |
| EAMM | 25.41% | 27.13% | 早期情感模型 |
| MakeItTalk | 19.37% | 20.46% | 缺乏顯式情緒建模 |
| Wav2Lip | 16.80% | 18.02% | 僅保留中性嘴型，完全無情感 |
