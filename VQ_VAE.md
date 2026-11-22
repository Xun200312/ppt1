# VQ-VAE (Vector Quantized Variational Autoencoder / 向量量化離散自編碼器)

## 1. 概述與定義
**VQ-VAE (Vector Quantized Variational Autoencoder)** 是一種將連續潛在表示離散化為碼本（Codebook）向量字典的自編碼框架。

在 3D 臉部動畫領域，直接回歸連續網格頂點座標常因 L2 均方誤差損失而產生「均值回歸（Regression to the Mean）」，導致面癱與表情呆滯。VQ-VAE 透過將臉部動態投影到離散運動碼本中，捕捉豐富的非語言先驗，成為克服面癱問題的代表性架構之一（如 CodeTalker）。

---

## 2. 核心技術細節與演進
- **經典 VQ-VAE 碼本查找**:
  - 編碼器輸出連續特徵 $l \in \mathbb{R}^D$。
  - 在碼本 $\mathcal{C} = \{c_k\}_{k=1}^K$ 中尋找歐氏距離最近的項目進行替換：
    $$\tilde{l} = \arg\min_{c_k \in \mathcal{C}} \|l - c_k\|_2$$
  - 訓練時採用梯度直通估計（Straight-Through Estimator, STE）。
- **EVQ-VAE (以情緒為中心的升級)**:
  - 由 [[2024_LGLDM]] (IEEE TVCG 2024) 提出。
  - **核心痛點**: 傳統碼本對所有表情共享空間，導致罕見情緒微表情被頻繁的中性口型淹沒（Codebook Collapse / Emotion Confusion）。
  - **解決方案**: 為每種情感類別 $E_k$ 配置獨立正交的碼本子空間 $c_k \in \mathcal{C}$，確保情緒微表情具備獨立的表示領域。

---

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
  - 離散先驗有效規避平滑面癱，動作生動有彈性。
  - 壓縮比高，便於與自回歸 Transformer 或擴散模型結合（如 VQ-Diffusion）。
- **缺點 / 挑戰**:
  - 碼本容量過大容易造成碼本崩塌（未被啟用的無效代碼）；容量過小則會導致細節丟失。

---

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :--- | :--- | :--- | :---: |
| 2023 | CodeTalker | 首創將離散運動先驗引入語音驅動 3D 臉部動畫 | [[CodeTalker]] |
| 2024 | LG-LDM (TVCG 2024) | 提出 EVQ-VAE：以情緒為中心的獨立正交碼本，解耦 8 類情緒微表情 | [[2024_LGLDM]] |
