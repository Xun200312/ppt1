# Wav2Vec 2.0

## 1. 概述與定義
**Wav2Vec 2.0** (Baevski et al., NeurIPS 2020) 是 Meta AI 提出的一種基於自監督學習（Self-Supervised Learning）的語音表徵框架。它透過對連續語音的潛在特徵進行量化（Gumbel-Softmax Vector Quantization）與對比損失（Contrastive Loss），在未標註語音資料上預訓練大模型。

在 3D 臉部動畫領域，早期里程碑模型如 [[FaceFormer]] (2022) 與 [[CodeTalker]] (2023) 均採用 Wav2Vec 2.0 的預訓練權重提取語音序列特徵。

## 2. 核心技術細節 / 資料結構
- **結構**:
  - 前端 CNN 編碼器：對原始波形音訊進行下採樣，輸出步長為 20ms 的特徵。
  - 後端 Transformer 架構：12 層 (Base) 或 24 層 (Large)。
- **特徵層選型**: 在 3D 臉部動畫中，研究普遍發現 Wav2Vec 2.0 較早至中間層特徵（如第 6~9 層）包含最豐富的音素與唇部運動資訊，而最後幾層更偏向語義。

## 3. 在 3D 臉部動畫中的表現評估 (HuBERT vs. Wav2Vec 2.0)
- **實證對比結論**:
  在 [[2025_RefFace]] 的消融實驗（Ablation on Audio Encoder）中，針對同一個模型結構在 BIWI 資料集進行嚴格對照：
  - 使用 **HuBERT**: LVE 達 **$3.8466 \times 10^{-4}$ mm**，FDD 達 **$2.9857 \times 10^{-5}$ mm**。
  - 使用 **Wav2Vec 2.0**: LVE 衰退至 **$4.2063 \times 10^{-4}$ mm**，FDD 衰退至 **$3.4654 \times 10^{-5}$ mm**。
  實驗證明 HuBERT 在 3D 臉部動畫任務中的唇部同步精度與面部動態表現均顯著優於 Wav2Vec 2.0。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | RefFace | 作為消融實驗對照組，證明其性能弱於 HuBERT | [[2025_RefFace]] |
| 2023 | CodeTalker | 採用 Wav2Vec 2.0 提取時序語音特徵驅動離散 Codebook | [[CodeTalker]] |
| 2022 | FaceFormer | 首次將 Wav2Vec 2.0 引入 Transformer 語音驅動 3D 臉部動畫 | [[FaceFormer]] |
