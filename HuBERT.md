# HuBERT (Hidden-Unit BERT)

## 1. 概述與定義
**HuBERT** 是由 Hsu 等人 (2021) 提出的自監督語音表徵學習模型（Self-Supervised Speech Representation Learning）。它透過對連續音訊進行 k-means 聚類生成離散偽標籤（Hidden Units），並採用類似 BERT 的遮蔽預測（Masked Prediction）機制進行預訓練。

在 3D 臉部動畫領域中，HuBERT 被廣泛用作音訊特徵提取骨幹，與 Wav2Vec 2.0 並列為主流。近年實證（如 [[2025_RefFace]]）進一步證實，相較於 Wav2Vec 2.0，HuBERT 能提供更佳的音素邊界與聲學發音資訊，顯著降低嘴唇運動誤差 (LVE)。

## 2. 核心技術細節 / 資料結構
- **模型結構**:
  - 前端：7 層卷積神經網路 (CNN Feature Extractor)，將原生音訊降採樣為特徵序列。
  - 後端：多層 Transformer Encoder（通常為 12 層或 24 層）。
- **特徵維度**:
  - 標準輸出特徵維度通常為 768 或 1024。
- **儲存與計算成本**:
  - 預訓練權重通常佔用 300MB 至 1GB 以上，直接部署於邊緣端或行動裝置會造成巨大儲存與記憶體壓力。

## 3. 在 3D 臉部動畫中的優缺點與實證對比
- **優點**:
  - **強大的音素與聲學對齊能力**: 對噪音、語音語調變化具有高度魯棒性。
  - **實證優於 Wav2Vec 2.0**: [[2025_RefFace]] 的控制變因實驗顯示，換用 HuBERT 後 LVE 從 4.2063 改善至 3.8466，FDD 從 3.4654 改善至 2.9857。
- **缺點 / 挑戰**:
  - **參數量巨大、計算繁重**: 在輕量化模型中往往成為系統瓶頸，促使 [[2025_DiffusionTalker]] 採用音訊編碼器蒸餾（HuBERT $\to$ CNN+BiLSTM）來化解體積負擔。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | RefFace | 凍結前段 CNN 與 Transformer 提取高精度語音特徵，實驗證實優於 Wav2Vec2 | [[2025_RefFace]] |
| 2025 | DiffusionTalker | 在教師模型中使用 HuBERT 提取音訊特徵，並蒸餾至小型 CNN+BiLSTM 編碼器 | [[2025_DiffusionTalker]] |
| 2023 | FaceXHuBERT | 使用自監督 HuBERT 提取語音表徵驅動表情 | [[FaceXHuBERT]] |
