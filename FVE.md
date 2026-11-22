# FVE (Face Vertex Error, 全臉頂點誤差)

## 1. 概述與定義
**FVE (Face Vertex Error)** 是評估 3D 臉部動畫中**全臉幾何形變精準度（Overall Facial Motion Accuracy）**的通用客觀指標。與聚焦於嘴部局部對齊的 [[LVE]] 不同，FVE 評估包含額頭、臉頰、下顎與眼部在內的整個 3D 臉部網格所有頂點。

## 2. 核心技術細節 / 數學定義
- **計算公式**:
  - **最大誤差 (FVE)**:
    $$\text{FVE} = \frac{1}{T} \sum_{t=1}^{T} \max_{v \in \mathcal{V}_{face}} \|\hat{\mathbf{v}}_t - \mathbf{v}_t^*\|_2$$
  - **平均誤差 (FVE-M)**:
    $$\text{FVE-M} = \frac{1}{T} \sum_{t=1}^{T} \frac{1}{|\mathcal{V}_{face}|} \sum_{v \in \mathcal{V}_{face}} \|\hat{\mathbf{v}}_t - \mathbf{v}_t^*\|_2$$
  其中 $\mathcal{V}_{face}$ 為全臉頂點集合（如 FLAME 拓撲中為 5023 個頂點），$T$ 為序列總幀數。
- **單位與方向**: 通常以毫米 (mm) 或 $\times 10^{-4}$ mm 標記，**數值越低越好 (↓)**。

## 3. 在 3D 臉部動畫中的定位
- **整體結構防畸變**: 檢驗模型是否在追求局部嘴型 (LVE) 時導致面部其他區域產生形變崩潰或不自然的拉伸。

## 4. 代表性基準表現 (3D MEAD-Test 基準)
| 模型 | FVE ($\times 10^{-4}$) ↓ | FVE-M ($\times 10^{-5}$) ↓ |
| :--- | :---: | :---: |
| **EmoPoseFace (Ours)** | **1.3626** | **1.2714** |
| EmoPoseFace (no HP) | 1.3889 | 1.2915 |
| UniTalker | 1.5648 | 1.5822 |
| E-FaceDiffuser | 1.5819 | 1.4538 |
| EmoVOCA | 1.6689 | 1.5366 |
| LG-LDM | 2.0211 | 1.9296 |
| EMOTE | 2.3650 | 2.3095 |
| DiffPoseTalk | 2.7901 | 2.5615 |
| E-FaceFormer | 2.8403 | 2.6918 |
