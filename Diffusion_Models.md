# Diffusion Models (擴散模型在 3D 臉部動畫的應用)

## 1. 概述與定義
**擴散模型 (Diffusion Probabilistic Models, DDPM / Score-based Generative Models)** 是一類基於熱力學反向擴散過程的生成模型。傳統的 3D 臉部動畫多採用確定性迴歸模型（如 CNN, Transformer, GRU），容易因訓練目標（如 L1/L2 損失）出現「回歸到均值 (Regression to the mean)」現象，造成動畫表情平淡、嘴型模糊且多樣性缺乏。

擴散模型將語音驅動 3D 臉部動畫視為條件隨機生成問題，利用噪聲注入與反向去噪，在保留嘴型準確度的同時大幅增強表情生動度與非確定性微表情（眨眼、頭部輕微晃動等）。

## 2. 核心技術細節 / 演算法架構
- **前向擴散 (Forward Process)**:
  $$q(x_t | x_0) = \mathcal{N}(x_t; \sqrt{\bar{\alpha}_t} x_0, (1 - \bar{\alpha}_t) \mathbf{I})$$
- **條件反向去噪 (Conditional Denoising Process)**:
  $$p_\theta(x_{t-1} | x_t, c) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t, c), \sigma_t^2 \mathbf{I})$$
  其中條件 $c$ 可包含音訊特徵 $F_a$、身份向量 $i$、情緒向量 $e$、參考特徵點 $r$ 或頭部姿態風格 $S_{pose}$ 等。

- **解碼主幹 (Motion Decoder) 範式演進**:
  1. **輕量級 GRU / RNN Backbone**: 如 [[2025_DiffusionTalker]]，參數量極小，搭配知識蒸餾達到 3600+ FPS。
  2. **空間-時間雙重注意力 (Spatial-Temporal Attention with FiLM)**: 如 [[2025_RefFace]]，透過 Transformer 跨注意力融合特徵點條件與音訊，並用 FiLM 層嵌入時間戳，在 10 步推論下達到高保真幾何還原。
  3. **雙分支解耦擴散架構 (Dual-branch Decoupled Diffusion)**: 如 [[2026_EmoPoseFace]]，為解決表情肌肉形變與頭部剛體旋轉的時序衝突，將去噪網路顯式拆分為兩個平行的 GRU 分支，分別獨立注入語音、情緒與 Pose 條件，配合速度與二階平滑損失，根除頭姿抖動。

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
  - **強大多樣性 (Diversity)**：有效模擬語音驅動表情的「一對多 (One-to-Many)」非確定性本質。
  - **細節表現力豐富**：有效減輕唇部與面部僵硬問題。
  - **多模態協同擴展性強**：可靈活接入頭部姿態、文字提示、情緒標籤與局部控制點。
- **缺點 / 挑戰**:
  - **推論步數與延遲瓶頸**：原生 DDPM 通常需要上百步迭代。近期研究（DiffusionTalker 2 步蒸餾、RefFace 10 步優化）正快速推動邊緣即時化。
  - **時空衝突與撕裂**：跨序列條件或表情/頭姿並行時，容易引發網格解離或抖動，需專門設計法向量約束（RefFace）或解耦雙分支（EmoPoseFace）。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 輸出表徵 | 去噪步數 / 速度 | 關鍵貢獻 | 論文連結 |
| :---: | :--- | :---: | :---: | :--- | :---: |
| 2026 | EmoPoseFace | FLAME Mesh + Head Pose | 1000 步 | 雙分支解耦擴散生成情緒表情與頭姿，消除頭部抖動 | [[2026_EmoPoseFace]] |
| 2025 | DiffusionTalker | Blendshapes | 2 步 (3632 FPS) | 個人化蒸餾 + 語音編碼器小型化 (45MB) | [[2025_DiffusionTalker]] |
| 2025 | RefFace | FLAME / BIWI Mesh | 10 步 (0.36s) | 特徵點表情條件引導 + 表面法向量損失 ($L_{snv}$) | [[2025_RefFace]] |
| 2024 | LG-LDM (TVCG) | FLAME Mesh | 1000 步 (8.36 FPS) | 局部到全局潛在擴散 (FDM) + 以情感為中心的離散碼本 (EVQ-VAE) | [[2024_LGLDM]] |
| 2023 | FaceDiffuser | Mesh | 長步數 (10.33 FPS) | 首批將擴散模型引入語音驅動 3D 臉部的代表作 | [[FaceDiffuser]] |
