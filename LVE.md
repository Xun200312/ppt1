# LVE (Lip Vertex Error, 嘴唇頂點誤差)

## 1. 概述與定義
**LVE (Lip Vertex Error)** 是評估語音驅動 3D 臉部動畫中**嘴型同步精準度（Lip Synchronization Accuracy）**最關鍵且最通用的客觀幾何評測指標。它衡量模型預測的嘴唇網格頂點與真實捕捉（Ground Truth）嘴唇頂點之間的歐氏距離偏差。

## 2. 核心技術細節 / 數學定義
- **計算公式**:
  - **最大誤差 (LVE)**:
    $$\text{LVE} = \frac{1}{T} \sum_{t=1}^{T} \max_{v \in \mathcal{V}_{lip}} \|\hat{\mathbf{v}}_t - \mathbf{v}_t^*\|_2$$
  - **平均誤差 (LVE-M)**:
    $$\text{LVE-M} = \frac{1}{T} \sum_{t=1}^{T} \frac{1}{|\mathcal{V}_{lip}|} \sum_{v \in \mathcal{V}_{lip}} \|\hat{\mathbf{v}}_t - \mathbf{v}_t^*\|_2$$
  其中 $\mathcal{V}_{lip}$ 代表定義在 FLAME 或臉部網格中的嘴唇區域頂點集合，$T$ 為總幀數。
- **單位與方向**: 通常以毫米 (mm) 或無量綱係數標註（如 $\times 10^{-4}$ mm 或 $\times 10^{-5}$），**數值越低越好 (↓)**。

## 3. 在 3D 臉部動畫中的定位與挑戰
- **重要性**: 直接決定觀看者感受到的說話是否「對嘴」。
- **盲點與爭議**: LVE 僅評估幾何距離，未考慮人類聽視覺感知的延遲容忍度；同時，強行追求極低的 LVE 容易使模型傾向於平滑嘴型，失去表情生動度。

## 4. 代表性基準表現 (Benchmark Reference)

### 3D-ETF 基準
| 模型 | 測試集 | LVE 表現 | 備註 |
| :--- | :--- | :---: | :--- |
| **DiffusionTalker (Ours-16)** | 3D-ETF | **$1.021 \times 10^{-5}$** | 目前該資料集最優 |
| **DiffusionTalker (Ours-2)** | 3D-ETF | $1.375 \times 10^{-5}$ | 2 步極速蒸餾版 |
| UniTalker-B | 3D-ETF | $1.727 \times 10^{-5}$ | 大規模統一模型 |
| EmoTalk | 3D-ETF | $2.347 \times 10^{-5}$ | Transformer 架構 |
| FaceFormer | 3D-ETF | $8.656 \times 10^{-5}$ | 自回歸基準 |

### BIWI-Test-A 基準
| 模型 | 測試集 | LVE 表現 ($\times 10^{-4}$ mm) ↓ | 備註 |
| :--- | :--- | :---: | :--- |
| **RefFace** | BIWI-Test-A | **3.8466** | 目前該基準最佳 SOTA |
| SelfTalk | BIWI-Test-A | 4.2485 | 自監督跨模態 |
| FaceDiffuser | BIWI-Test-A | 4.2986 | 擴散模型 |
| FaceXHuBERT | BIWI-Test-A | 4.5673 | HuBERT 驅動 |
| CodeTalker | BIWI-Test-A | 4.7923 | 離散動作先驗 |
| FaceFormer | BIWI-Test-A | 5.3027 | 自回歸 Transformer |
| VOCA | BIWI-Test-A | 6.5563 | 經典 CNN 基準 |

### 3D MEAD-Test 情感基準
| 模型 | 測試集 | LVE ($\times 10^{-4}$) ↓ | LVE-M ($\times 10^{-5}$) ↓ |
| :--- | :--- | :---: | :---: |
| **EmoPoseFace** | 3D MEAD | **1.3211** | **3.4583** |
| EmoPoseFace (no HP) | 3D MEAD | 1.3637 | 3.4984 |
| UniTalker | 3D MEAD | 1.4900 | 3.8992 |
| E-FaceDiffuser | 3D MEAD | 1.5686 | 4.0079 |
| EmoVOCA | 3D MEAD | 1.6646 | 4.3792 |
| LG-LDM | 3D MEAD | 2.0144 | 5.4633 |
| EMOTE | 3D MEAD | 2.3396 | 5.9115 |
