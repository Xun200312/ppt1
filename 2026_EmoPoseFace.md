# [2026] EmoPoseFace: Head Pose Aware Speech-Driven 3D Emotional Facial Animation Using Latent Diffusion

- **Title**: EmoPoseFace: Head Pose Aware Speech-Driven 3D Emotional Facial Animation Using Latent Diffusion
- **Authors**: Xin Zhao, Ju Dai, Feng Zhou, Haofei Wang, Zhen Song, Aimin Hao, Hong Qin, Yang Gao
- **Venue**: IEEE Transactions on Visualization and Computer Graphics (TVCG), Vol. 32, No. 8, August 2026
- **Year**: 2026
- **Code**: [GitHub Repository](https://github.com/zaqwsx6/EmoPoseFace)
- **DOI**: 10.1109/TVCG.2026.3702133
- **Raw PDF**: [[raw/paper/2026_EmoPoseFace.pdf]]
- **Input Modality**: [[HuBERT]] (HuBERT-base-ls960 音訊特徵), 情感標籤 (7 類基本情緒 One-hot / $E_k$), 姿勢風格編碼 $S_{pose}$ (由頭部運動參數經 Transformer Style Encoder 抽取)
- **Output Representation**: [[FLAME]] 混合表徵：稠密面部網格頂點形變 $M_0 \in \mathbb{R}^{N \times 15069}$ ($5023 \times 3$) + 頭部旋轉姿勢向量 $B_0 = \rho^{1:3} \in \mathbb{R}^{N \times 3}$
- **Primary Method**: [[Diffusion_Models]] (雙分支 GRU 去噪架構解耦面部與頭姿), [[Facial_Motion_Editing]] (GL-FFE 全局-局部微觀編輯模組 + FMSB 動作平滑混合演算法)

---

## 1. 一句話核心貢獻 (One-line Takeaway)
> 首次在擴散模型架構下實現「語音-情感-頭部姿態（Head Pose）」的同步協同生成，透過雙分支 GRU 架構徹底解耦面部表情與頭部晃動，並提出基於 FACS AU 的 GL-FFE 編輯模組與 FMSB 平滑演算法，開創了 3D Mesh 上的時空細粒度動作編輯與情感增強技術。

---

## 2. 解決的研究問題 (Problem Formulation)
- **現存痛點**:
  1. **情感表達缺乏頭部動態配合 (Lack of Head Pose)**: 過去的情感臉部動畫研究（如 EMOTE, EmoTalk, LG-LDM）僅專注於臉部網格，忽略了心理學所證實的「頭部姿態與情感節奏高度相關」之事實，缺乏自然點頭或偏頭導致真實感大幅削弱。
  2. **頭姿與表情聯合建模時的干擾與抖動 (Jitter & Conflict)**: 現有少數嘗試加入頭姿的方法（如 DiffPoseTalk 或微調的 E-FaceDiffuser）常因表情與頭部運動頻率不同，導致面部痙攣或嚴重的幀間抖動（Jitter）。
  3. **3D Mesh 缺乏時空細粒度編輯能力 (Lack of Fine-grained Editing)**: 過去的動畫編輯多停留在全域情感標籤替換（EMOTE 轉換生硬）或依賴 ARKit Blendshapes 粗粒度控制（CafeTalk），無法在 3D 稠密網格上針對眨眼、皺眉等局部肌肉進行平滑的關鍵影格漸變控制。
- **核心出發點**:
  - 構建雙分支條件擴散架構，分別為面部表情（Exp Branch）與頭部旋轉（Pose Branch）設計獨立的條件注入與損失約束，兼顧嘴唇同步與節奏律動。
  - 將 51 維 ARKit Blendshapes 重新映射組合為 15 個嚴格語義的 FACS 動作單元 (AU)，設計過渡窗函數 FMSB 確保局部編輯不破壞唇型同步。

---

## 3. 核心方法與技術架構 (Methodology)

### 3.1 混合運動表徵 (Hybrid Motion Representation)
模型輸出聯合運動參數 $X_0^{1:N} = (M_0^{1:N}, B_0^{1:N})$：
- **面部幾何**: $M_0^{1:N} \in \mathbb{R}^{N \times 15069}$，由 FLAME 混合形狀函數 $FM(\beta=0, \sigma, \rho^{4:6})$ 計算出 5023 個頂點的三維形變。
- **頭部姿態**: $B_0^{1:N} = \rho^{1:3} \in \mathbb{R}^{N \times 3}$，對應 FLAME 中的頸部與頭部旋轉參數。

### 3.2 雙分支擴散解噪架構 (Dual-branch Latent Diffusion)
- **Pose Style Encoder**:
  使用 4 層 Transformer Encoder 提取參考視頻的頭部運動參數 $B_0^{1:N}$，經 Average Pooling 得到姿勢風格向量 $S_{pose}$。
- **解碼架構**:
  - **面部表情分支 (Exp GRU)**: 輸入特徵拼接 $[E_{exp}(M_t^{1:N}), E_{audio}(A^{1:N}), \text{MLP}(t)]$。
  - **頭部姿態分支 (Pose GRU)**: 輸入特徵拼接 $[E_{pose}(B_t^{1:N}), E_{audio}(A^{1:N}), \text{MLP}(t), S_{pose}]$。
  - 兩分支均採用 2 層 GRU 進行迭代去噪，並同時受到情感嵌入 $E_k$ 的條件監督。
- **聯合損失函數**:
  $$\mathcal{L} = \lambda_{simple} \mathcal{L}_{simple} + \lambda_{vel} \mathcal{L}_{vel} + \lambda_{smooth} \mathcal{L}_{smooth} + \mathcal{L}_{head}$$
  其中 $\mathcal{L}_{head} = \mu_{simple} \mathcal{L}_{simple}^{head} + \mu_{vel} \mathcal{L}_{vel}^{head} + \mu_{smooth} \mathcal{L}_{smooth}^{head}$，對表情與頭姿的速度（Velocity）與平滑度（Smoothness）實施雙重二階時序約制，有效根除頭部抖動。

### 3.3 全局-局部細粒度編輯 (GL-FFE & FMSB)
1. **AU 與 ESAU 表徵構建**:
   - 將 ARKit 51 維 Blendshapes 投影至 FLAME 網格，重組為 15 個符合 FACS 規範的 Action Units (AU)。
   - **中層 ESAU (Emotion-Specific AU)**: 將 7 種基礎情緒對應為特定的 AU 線性組合（如 Happy 為 AU6(100%)+AU12(80%)；Sad 為 AU1+AU4+AU15）。
   - **全局情緒強度增強**:
     $$X_k^{1:N} = \hat{X}_0^{1:N} + W_{emo} \Delta ESAU_k, \quad W_{emo} \in [0, 1]$$
2. **FMSB 局部動作漸變演算法 (BlendMorph Algorithm)**:
   針對特定關鍵影格 $I$、局域動作持續時間 $\tau$ 與過渡上升/下降窗長 $w$，定義平滑加權函數 $\phi(i; I, w, \tau)$：
   $$\phi(i; I, w, \tau) = \begin{cases} \frac{i-(I-w)}{w+1} & I-w \le i < I \\ 1 & I \le i < I+\tau \\ 1 - \frac{i-(I+\tau-1)}{w+1} & I+\tau \le i < I+\tau+w \\ 0 & \text{otherwise} \end{cases}$$
   $$M'(i) = M(i) + \phi(i; I, w, \tau) \cdot W_{local} \Delta b$$
   可自由編輯眨眼 (Blink)、瞇眼 (Squint eyes)、挑眉 (Frown) 等動作，且完全不干擾嘴唇發音基線。

---

## 4. 實驗設定與評測表現 (Experiments & Benchmarks)

### 4.1 實驗設定
- **資料集**: 3D [[MEAD]]（48 位演員、7 種情緒、3 級強度，經 EMOCAv2 重構為 FLAME 網格，並對頭姿使用 Kalman 濾波平滑）。
- **硬體與超參數**: NVIDIA RTX 4090 GPU，Adam 優化器，lr=1e-4，1000 擴散步數。
- **評測指標**:
  - 嘴唇同步: [[LVE]] (最大誤差), LVE-M (平均誤差)
  - 全臉幾何: [[FVE]] (最大誤差), FVE-M (平均誤差)
  - 上半臉情緒: [[EVE]] (情緒區域頂點誤差)
  - 頭部節奏: [[BA]] (Beat Alignment，動作節拍與語音節奏一致性)

### 4.2 主要量化對比 (3D MEAD-Test 基準)
| 類別 | 方法 | FVE ↓ ($\times 10^{-4}$) | EVE ↓ ($\times 10^{-6}$) | LVE ↓ ($\times 10^{-4}$) | FVE-M ↓ ($\times 10^{-5}$) | LVE-M ↓ ($\times 10^{-5}$) | BA ↑ |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **無頭姿 (w/o HP)** | E-FaceFormer | 2.8403 | 3.2024 | 2.8360 | 2.6918 | 7.7096 | N/A |
| | EMOTE | 2.3650 | 6.0703 | 2.3396 | 2.3095 | 5.9115 | N/A |
| | LG-LDM | 2.0211 | 2.5885 | 2.0144 | 1.9296 | 5.4633 | N/A |
| | UniTalker | 1.5648 | 5.3431 | 1.4900 | 1.5822 | 3.8992 | N/A |
| | ProbTalk3D | 2.2919 | 3.0356 | 2.2870 | 2.1163 | 5.9515 | N/A |
| | EmoVOCA | 1.6689 | 2.4302 | 1.6646 | 1.5366 | 4.3792 | N/A |
| | **Ours (no HP)** | **1.3889** | **2.4837** | **1.3637** | **1.2915** | **3.4984** | N/A |
| **含頭姿 (w/ HP)** | E-FaceDiffuser | 1.5819 | 2.6221 | 1.5686 | 1.4538 | 4.0079 | 0.29 |
| | DiffPoseTalk | 2.7901 | 3.0127 | 2.7858 | 2.5615 | 6.8591 | 0.28 |
| | **EmoPoseFace (Ours)** | **1.3626** | **2.3783** | **1.3211** | **1.2714** | **3.4583** | **0.33** |

> **數據解讀**: 
> 1. 相較於先前最優的 EmoVOCA，EmoPoseFace 在 LVE 與 FVE 上取得約 **20% ~ 25% 的大幅領先**。
> 2. 在含頭姿生成上，BA (Beat Alignment) 達到最優的 **0.33**，同時徹底消除了 E-FaceDiffuser 嚴重的頭部高頻抖動問題。

### 4.3 消融實驗關鍵發現 (Ablation Insights on MEAD-Test)
- **去掉頭部損失 ($w/o \mathcal{L}_{head}$)**: 模型僅監督臉部時，全臉與嘴型誤差上升（FVE 1.5166 vs 1.3626），證明適當的頭部約束有助於整體運動韻律的學習。
- **單分支 GRU vs 雙分支 GRU**: 單分支架構在 FVE (1.6122)、EVE (2.9399)、LVE (1.6020) 上全面劣化，證實**頭部剛體旋轉與面部非剛體形變必須在網路架構上顯式解耦**。
- **雙分支 Transformer vs 雙分支 GRU**: 在此時序任務中，雙分支 GRU (FVE 1.3626) 優於雙分支 Transformer (FVE 1.5231)，GRU 能更穩定地避免過擬合。

---

## 5. 優勢與局限性 (Strengths & Limitations)

### 優勢
1. **多模態全息表現力**: 唯一同時精確控制「語音嘴型對齊 + 7類情緒微表情 + 與語義律動同步的自然頭部姿態」的模型。
2. **首創 3D Mesh 細粒度編輯**: GL-FFE 允許動畫師以物理過渡窗任意加入瞬態動作（眨眼、挑眉），極大提升下游 VR / 遊戲製作價值。
3. **無抖動的頭部運動**: 結合雙分支解耦與時序二階平滑損失，解決擴散模型預測剛體姿勢時常見的抽搐問題。

### 局限性
1. **推論步數仍為傳統擴散步數**: 採用標準 1000 步擴散過程，未像 [[2025_DiffusionTalker]] 引入步數蒸餾，推論速度相對受限。
2. **單目重建標籤雜訊**: MEAD 的 3D 偽真值源自單目影片擬合（EMOCAv2），即使經過卡爾曼濾波，資料集中仍殘存細微雜訊。

---

## 6. 關聯脈絡 (Relations)
- **啟發與延伸自 (Builds on)**:
  - [[FaceDiffuser]] / DiffPoseTalk: 繼承擴散模型生成語音頭像的思路，但提出雙分支結構化解兩者衝突。
  - [[EMOTE]] / EmoTalk: 繼承 MEAD 的情感建模研究，但克服了其缺乏頭部姿態與動作轉換生硬的缺陷。
- **對比與互補 (Compared & Complementary to)**:
  - [[2025_DiffusionTalker]]: DiffusionTalker 專精於「極速蒸餾（2步/45MB/3600FPS）與輕量化」，而 EmoPoseFace 專精於「全維度表現力（頭姿+情緒+局部細粒度編輯）」。兩者代表了 2025-2026 年擴散 3D 臉部動畫的兩大主要演進方向：**極致效率部署** vs **極致全息可控**。
  - [[2025_RefFace]]: RefFace 透過特徵點外部條件引導表情，而 EmoPoseFace 透過 AU 與 ESAU 實現語義層級的精細編輯。
