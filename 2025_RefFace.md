# [2025] RefFace: Generating 3D Speech-Driven Animation with Reference

- **Title**: RefFace: Generating 3D Speech-Driven Animation with Reference
- **Authors**: Zhuoliang Jiang, Lanfang Dong
- **Venue**: 2025 5th International Conference on Artificial Intelligence, Automation and High Performance Computing (AIAHPC 2025)
- **Year**: 2025
- **Code**: N/A
- **DOI**: 10.1109/AIAHPC66801.2025.11290310
- **Raw PDF**: [[raw/paper/2025_RefFace.pdf]]
- **Input Modality**: [[HuBERT]] (音訊特徵提取), 48 個臉部特徵點 (Facial Landmarks) 作為表情參考
- **Output Representation**: [[FLAME]] / [[BIWI]] 稠密 3D 網格頂點形變量 (Mesh Displacements)
- **Primary Method**: [[Diffusion_Models]] (10 步條件去噪), 空間/時間雙重注意力機制 (Spatial-Temporal Attention with FiLM), 二階段訓練與法向量損失 (Surface Normal Vector Loss)

---

## 1. 一句話核心貢獻 (One-line Takeaway)
> 首次將臉部特徵點（Landmarks）作為獨立條件引入擴散模型，實現音訊精準驅動嘴唇動作、參考特徵點精確控制上半臉表情的解耦生成，並提出表面法向量損失（$L_{snv}$）徹底解決跨序列表情遷移時的面部網格撕裂與不連續問題。

---

## 2. 解決的研究問題 (Problem Formulation)
- **痛點**:
  1. **語音與非言語表情解耦困難**: 語音訊號主要決定唇形與發音動作，但豐富的情感與微表情（如揚眉、眨眼、挑釁等）無法單純由語音推導。
  2. **傳統身份編碼泛化性差**: 過去方法（如 FaceFormer, CodeTalker）多使用 One-hot 身份向量來控制說話風格，嚴重過擬合於訓練集發音人，無法遷移至全新身份。
  3. **跨序列控制引發面部解離 (Surface Dissociation)**: 當上半臉表情來自參考序列、唇形來自語音序列時，不同區域的預測頂點在邊界處容易失調，導致臉部三維網格斷裂與法向量畸變。
- **核心出發點**:
  - 利用預先設計或抽取的 48 個上半臉特徵點作為 Reference 條件，在保留語音對齊嘴型的同時，賦予影視與遊戲動畫師對表情的精準控制能力。
  - 設計兩階段訓練（同序列重構 $\to$ 跨序列解耦遷移），並引入表面法向量約束保證幾何平滑。

---

## 3. 核心方法與技術架構 (Methodology)

### 3.1 整體架構 (Spatial-Temporal Diffusion Network)
RefFace 將去噪過程建構於 3D 頂點形變空間 $m_t \in \mathbb{R}^{B \times F \times V \times 3}$：
1. **維度分離投影 (Dimension-wise Input Projection)**:
   - 突破傳統將 3D 頂點視為單一整體的做法，先轉置為 $3 \times V$，分別對 $X, Y, Z$ 三個維度進行線性投影與卷積提取，使網路更能捕捉單一空間軸向上的幾何形變特徵。
2. **空間注意力模組 (Spatial Attention Block)**:
   - 採用 Transformer Encoder-Decoder 架構。將參考特徵點 $r$ 經 Encoder 編碼為 $z_r$，以動作特徵 $z_m$ 作為 Query，跨注意力查詢參考特徵，輸出空間特徵 $z_s = \text{Decoder}(z_m, \text{Encoder}(z_r))$。
3. **時間注意力模組 (Temporal Attention Block with FiLM)**:
   - 針對臉部運動主要依賴當前影格的特性，先透過帶有局域遮罩 $X_{i,j}$（$|i-j| \le 2$）的 Cross-Attention 將空間特徵與當前影格音訊 $z_a$ 對齊。
   - 接著使用自注意力（帶有防止偷看未來的因果遮罩 $Y_{i,j}$）建模影格間時序關聯。
   - 在各層之間嵌入基於擴散時間戳 $t$ 的 **FiLM (Feature-wise Linear Modulation)** 條件層調製特徵。

### 3.2 兩階段訓練策略 (Two-Stage Training Strategy)
- **第一階段（單序列基本動作學習）**:
  - 音訊與參考特徵點來自同一段動態序列。
  - 損失函數包含頂點均方誤差、時序速度損失 $L_{vel}$ 與平滑損失 $L_{smo}$：
    $$L_1 = L_{vert} + \lambda_{vel} L_{vel} + \lambda_{smo} L_{smo}$$
- **第二階段（跨序列解耦與法向量平滑）**:
  - 分別採樣音訊序列 $m_0^i$ 與表情參考序列 $m_0^j$。
  - 使用唇部區域遮罩 $\text{mask}_{lip}$ 與上半臉遮罩 $\text{mask}_{up}$ 計算區域重構損失 $L_{reg}$。
  - **表面法向量損失 (Surface Normal Vector Loss, $L_{snv}$)**：
    將預測形變加回範本網格 $T$，計算每個三角形網格面的法向量 $\hat{n}_0 = (\hat{v}_3 - \hat{v}_1) \times (\hat{v}_2 - \hat{v}_1)$，最大化與真實法向量 $n_0$ 的餘弦相似度：
    $$L_{snv} = \sum_{B} \sum_{F} \sum_{faces} \cos(\hat{n}_0, n_0)$$
    有效消除跨區域形變時的幾何斷層。

---

## 4. 實驗設定與評測表現 (Experiments & Benchmarks)

### 4.1 訓練與測試資料集
- **資料集**: [[BIWI]] (主要評測)、[[VOCASET]] 與 [[MEAD]] (透過 EMOTE 擬合出 FLAME 拓撲用於多表情訓練)。
- **特徵點設定**: 採用 FLAME 68 特徵點中排除 20 個唇部特徵點後的 **48 個上半臉特徵點**；BIWI 則選取對應的 48 個上半臉頂點。
- **推論效率**: 擴散模型訓練時使用 1000 步，推論時僅需 **10 步**。

### 4.2 主要量化對比 (BIWI-Test-A 基準)
| Method | LVE ↓ ($\times 10^{-4}$ mm) | FDD ↓ ($\times 10^{-5}$ mm) | Inference Time (s) ↓ |
| :--- | :---: | :---: | :---: |
| VOCA (CVPR 2019) | 6.5563 | 8.1816 | 0.1600 |
| MeshTalk (ICCV 2021) | 5.9181 | 5.1025 | 0.2149 |
| FaceFormer (CVPR 2022) | 5.3027 | 4.6376 | 0.2974 |
| CodeTalker (CVPR 2023) | 4.7923 | 4.1035 | 1.5780 |
| FaceXHuBERT (2023) | 4.5673 | 4.9675 | 0.5219 |
| FaceDiffuser (2023) | 4.2986 | 3.9098 | 0.5500 |
| SelfTalk (ACM MM 2023) | 4.2485 | 3.5760 | **0.1240** |
| **RefFace (Ours)** | **3.8466** | **2.9857** | **0.3640** |

> **說明**：RefFace 在 BIWI 基準上創下當前最佳的 LVE (3.8466) 與 FDD (2.9857)，且 10 步推論時間僅 0.364 秒，達到即時生成水準。

### 4.3 人類主觀評測 (Perceptual User Study)
與各基準模型進行 A/B 對比測試（66 名受試者，含 30% 動畫專家）：
- **vs. FaceFormer**: 70.5% 偏好 RefFace 的嘴型，76.4% 偏好整體表現。
- **vs. CodeTalker**: 63.1% 偏好嘴型，73.6% 偏好整體表現。
- **vs. FaceDiffuser**: 62.1% 偏好嘴型，72.4% 偏好整體表現。
- **vs. EMOTE**: 84.6% 偏好 RefFace 的嘴型與整體品質。

### 4.4 消融實驗發現 (Ablation Insights on BIWI)
| 變體設定 | LVE ↓ ($\times 10^{-4}$ mm) | FDD ↓ ($\times 10^{-5}$ mm) | 結論 |
| :--- | :---: | :---: | :--- |
| **RefFace-Proposed (完整)** | **3.8466** | **2.9857** | 表現最佳 |
| RefFace-Wav2Vec | 4.2063 | 3.4654 | **HuBERT 顯著優於 Wav2Vec 2.0** |
| RefFace-Fused (未拆分維度) | 4.0347 | 4.4729 | 獨立處理 X, Y, Z 三軸有助於空間注意力解碼 |
| RefFace-Omitted (無參考特徵點) | 4.0885 | 3.3482 | 無特徵點時嘴型仍保持水準，但動態表現下降 |
| RefFace-GRU | 4.2325 | 4.8641 | Transformer 解碼器比 GRU 時序更佳 |
| RefFace-RNN | 4.4177 | 5.2876 | RNN 表現最差 |
| **法向量損失 ($L_{snv}$) 消融** | - | - | 視覺對比證明無 $L_{snv}$ 時臉部邊界會發生撕裂與網格錯位 |

---

## 5. 優勢與局限性 (Strengths & Limitations)

### 優勢
1. **精準的表情可控性**: 解決語音驅動動畫中「非語言表情失控或平淡」的痛點，允許透過少量 3D 特徵點直觀編輯上半臉神態。
2. **優異的幾何連續性**: 透過原創的表面法向量損失（$L_{snv}$），實現了高品質的跨序列表情遷移（如將憤怒表情移植到中性發音音訊上）。
3. **擴散模型加速**: 將推論步數精簡至 10 步，在保持 Dense Mesh 生成精度的同時實現了 0.36s 的高效生成。

### 局限性
1. **需要特徵點輸入**: 依賴外部預設或抽取的 48 個特徵點軌跡；若缺乏特徵點序列，則退化為一般語音驅動模式。
2. **計算開銷仍高於純參數化模型**: 直接預測萬級頂點（BIWI 達 23,370 頂點），運算量與顯存消耗大於直接預測 Blendshapes 係數的模型（如 [[2025_DiffusionTalker]]）。

---

## 6. 關聯脈絡 (Relations)
- **啟發與對比自 (Builds on & Competes with)**:
  - [[2025_DiffusionTalker]]: 兩者均發表於 2025 年並聚焦「擴散模型加速」，DiffusionTalker 走「Blendshapes + 知識蒸餾（2步/45MB/無標籤）」，RefFace 走「稠密 Mesh + 特徵點條件指引（10步/法向量損失）」。
  - [[FaceDiffuser]]: 先前具代表性的 3D 臉部擴散模型，但步數多、推論慢（0.55s 且 LVE 4.2986）。
  - [[FaceFormer]] & [[CodeTalker]]: 被 RefFace 在 BIWI 基準上全面超越的確定性與離散先驗代表作。
  - [[HuBERT]]: 實驗直接證實 HuBERT 在 3D 臉部動畫中全面優於 [[Wav2Vec2]]。
