# Knowledge Distillation (知識蒸餾在 3D 臉部動畫的應用)

## 1. 概述與定義
**知識蒸餾 (Knowledge Distillation, KD)** 是一種將龐大、高表現但計算昂貴的「教師模型 (Teacher Model)」中的知識，轉移至輕量、緊湊的「學生模型 (Student Model)」中的技術。

在 3D 臉部動畫領域，知識蒸餾主要被用來解決兩大瓶頸：
1. **擴散模型的步數壓縮 (Step Distillation)**：藉由漸進式折半蒸餾（Progressive Distillation），將原本需要 16~100 步的擴散反向過程壓縮至 1~2 步。
2. **語音特徵抽取器瘦身 (Feature Encoder Compression)**：主流方法大量採用參數量巨大且計算冗餘的自監督音訊模型（如 HuBERT, Wav2Vec 2.0），透過特徵對齊損失蒸餾成小型 CNN/LSTM 網路，大幅縮減終端推論資源消耗。

## 2. 核心技術細節 / 演算法架構
- **漸進式步數蒸餾 (Progressive Step Distillation)**:
  - 核心機制：使學生模型的一步預測匹配教師模型的兩步預測（$2n \to n$）。
  - 損失函數：
    $$\mathcal{L}_{dis} = \max\left(\frac{\alpha_\tau^2}{\sigma_\tau^2}, 1\right) \|\hat{x}^{te} - \hat{x}^{st}\|^2$$
- **特徵對齊蒸餾 (Feature Alignment Distillation)**:
  - 學生小模型與教師大模型在潛在空間計算餘弦相似度或 MSE 損失，確保聲學特徵表徵能力不丟失：
    $$\mathcal{L}_{ae} = 1 - \frac{F_a^L \cdot F_a^S}{\|F_a^L\|_2 \|F_a^S\|_2}$$

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
  - **極限加速與小型化**: 可實現 8x 以上的推論加速與 80% 以上的模型體積縮減。
  - **保留高階先驗**: 學生模型可保留大模型學得的豐富動態分佈。
- **缺點 / 挑戰**:
  - **訓練流程複雜**: 通常需要先收斂教師模型，再多輪疊代訓練學生模型。
  - **潛在精度微損**: 極端步數下（如 1~2 步），嘴唇微小動作精準度可能會有些微退化（需額外的對比或邊界約束補償）。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | DiffusionTalker | 結合步數蒸餾 (16$\to$2步)、HuBERT音訊蒸餾 (45MB) 與個人化增強對比學習 | [[2025_DiffusionTalker]] |
