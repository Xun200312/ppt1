# 3D Facial Animation Wiki Index (知識庫總索引)

本索引是整個 3D 臉部動畫知識庫的中央地圖，由 AI Agent 隨論文的收錄與研究的深入自動更新維護。

---

## 1. 論文總覽 (Papers)

| 年份 | 簡稱 | 完整論文名稱 | 核心架構 | 輸出表徵 | 筆記連結 |
| :---: | :---: | :--- | :--- | :--- | :---: |
| 2026 | ESGaussianFace | ESGaussianFace: Emotional and Stylized Audio-Driven Facial Animation via 3D Gaussian Splatting | [[Neural_Rendering]] (3DGS, 69 FPS) + [[Multi_Stage_Training]] | [[3DGS]] 點雲 + [[Triplane]] | [[2026_ESGaussianFace]] |
| 2026 | EmoPoseFace | EmoPoseFace: Head Pose Aware Speech-Driven 3D Emotional Facial Animation Using Latent Diffusion | [[Diffusion_Models]] (雙分支 GRU 解耦) + [[Facial_Motion_Editing]] | [[FLAME]] Mesh + [[Head_Pose]] | [[2026_EmoPoseFace]] |
| 2025 | RefFace | RefFace: Generating 3D Speech-Driven Animation with Reference | [[Diffusion_Models]] (Spatial-Temporal Attn + FiLM) | [[FLAME]] / [[BIWI]] 稠密 Mesh | [[2025_RefFace]] |
| 2025 | DiffusionTalker | DiffusionTalker: Efficient and Compact Speech-Driven 3D Talking Head via Personalizer-Guided Distillation | [[Diffusion_Models]] + [[Knowledge_Distillation]] | [[Blendshapes]] | [[2025_DiffusionTalker]] |
| 2024 | LG-LDM | Expressive 3D Facial Animation Generation via Local-to-Global Latent Diffusion | [[Diffusion_Models]] (LG-LDM / FDM) + [[VQ_VAE]] (EVQ-VAE) | [[FLAME]] 5023 頂點 Mesh | [[2024_LGLDM]] |

---

## 2. 幾何表徵與參數模型 (Representations)

| 表徵名稱 | 類型 | 參數空間 / 自由度 | 常用場景 | 筆記連結 |
| :--- | :--- | :--- | :--- | :---: |
| [[3DGS]] | 顯式 3D 高斯橢球點雲輻射場 | 中心 $\mu$, 尺度 $s$, 旋轉 $q$, 球諧 $sh$, 透明度 $\alpha$ | 超高速 (60~80 FPS) 照片級多視角神經渲染 | [[3DGS]] |
| [[Triplane]] | 正交三平面空間特徵 | $3 \times (R \times R \times C)$ 張量 | 空間幾何特徵約束、3DGS 規範人臉解碼 | [[Triplane]] |
| [[FLAME]] | 統計三維人臉模型 | 5023 頂點、形狀/表情/姿勢解耦參數 | 3D 稠密臉部動畫通用拓撲標準 | [[FLAME]] |
| [[Blendshapes]] | 參數化動作基底 (FACS) | 低維係數 (通常 52 維) | 即時渲染、遊戲引擎整合、輕量化動作生成 | [[Blendshapes]] |
| [[Action_Units]] | 肌肉解剖動作單元 (FACS) & ESAU | 15 個核心 AU、7 類情感組合 | 階層式情緒增強、局部微表情無衝突編輯 | [[Action_Units]] |
| [[Facial_Landmarks]] | 稀疏關鍵幾何點 | 48~68 點 3D 座標 | 外部表情條件控制、語音/非語言特徵解耦 | [[Facial_Landmarks]] |
| [[Head_Pose]] | 剛體旋轉與律動姿態 | $\rho^{1:3} \in \mathbb{R}^3$ (尤拉角/旋轉向量) | 說話節奏增強、恐怖谷消除、自然互動 | [[Head_Pose]] |

---

## 3. 演算法架構 (Methods)

| 架構類別 | 代表範式 | 解決的核心難題 | 筆記連結 |
| :--- | :--- | :--- | :---: |
| [[Neural_Rendering]] | 3DGS 微分光柵化 / 空間注意力 (ESAM) | 攻克 NeRF 渲染延遲，實現 70 FPS 照片級情感多視角與風格化合成 | [[Neural_Rendering]] |
| [[Diffusion_Models]] | DDPM / 雙分支解耦 / 條件生成 | 解決回歸均值，增強表情與頭部姿態的多樣性與時空協同 | [[Diffusion_Models]] |
| [[Knowledge_Distillation]] | 漸進式步數折半蒸餾 + 特徵對齊 | 解決擴散模型推論延遲與音訊編碼器儲存冗餘 | [[Knowledge_Distillation]] |
| [[Facial_Motion_Editing]] | GL-FFE 全局-局部編輯 + FMSB 平滑演算法 | 解決 3D Mesh 動作跳變，實現局部肌肉平滑漸變與全域情緒無失真增強 | [[Facial_Motion_Editing]] |
| [[Multi_Stage_Training]] | 中性發音 $\to$ 情感形變 $\to$ 藝術風格遷移 | 解耦複雜多模態學習目標，防止嘴型對齊被情感與風格特徵破壞 | [[Multi_Stage_Training]] |
| [[VQ_VAE]] | 向量量化離散自編碼器 / 情感碼本 (EVQ-VAE) | 解決連續頂點回歸均值面癱，劃分正交獨立情感碼本捕捉微表情 | [[VQ_VAE]] |

---

## 4. 輸入模態與特徵編碼 (Modalities & Audio Encoders)

| 特徵 / 編碼器 | 類型 | 特性描述 | 筆記連結 |
| :--- | :--- | :--- | :---: |
| [[HuBERT]] | 自監督語音表徵 (SSL) | 具備極強音素分界與聲學捕捉能力，實證證明在 LVE 與 FDD 指標上顯著優於 Wav2Vec2 | [[HuBERT]] |
| [[Wav2Vec2]] | 自監督語音表徵 (SSL) | 早期經典語音編碼器 (FaceFormer, CodeTalker)，為現代音訊驅動研究奠定基礎 | [[Wav2Vec2]] |
| [[DeepSpeech]] | 經典端到端 ASR 聲學特徵 | 維度緊湊輕量，常與空間注意力結合驅動唇形 | [[DeepSpeech]] |

---

## 5. 基準資料集 (Datasets)

| 資料集名稱 | 標註形式 | 語言 / 說話人數量 | 常用於測試評測指標 | 筆記連結 |
| :--- | :--- | :--- | :---: | :---: |
| [[MEAD]] | 多視角 2D 轉 3D FLAME 拓撲 | 英語 / 60 位演員 (8 種情緒 3 級強度) | 情感動畫全方位 Benchmark (幾何 Mesh 榜與可視化渲染榜) | [[MEAD]] |
| [[RAVDESS]] | 廣播級 2D 情感視聽錄影 | 英語 / 24 位專業演員 (8 種情緒) | 情感解耦驗證、跨資料集零樣本泛化基準 | [[RAVDESS]] |
| [[BIWI]] | 稠密掃描 (23370 頂點) | 英語 / 14 位受試者 | LVE, FDD (高密度 Mesh SOTA 主戰場) | [[BIWI]] |
| [[3D_ETF]] | Blendshapes / FLAME | 英語 / 24 位專業演員 (8 種情緒) | EVE, LVE, FDD (情感表情基準) | [[3D_ETF]] |
| [[VOCASET]] | 4D 掃描 / 5023 頂點 FLAME | 英語 / 12 位受試者 | LVE, FVE (經典 Zero-Shot 評測與基礎訓練) | [[VOCASET]] |
| [[BEAT]] | 全身骨架 + 臉部 Blendshapes | 多語 / 30 位說話人 (大規模情感) | 大規模語音表情預訓練 | [[BEAT]] |

---

## 6. 評測指標 (Metrics)

| 指標縮寫 | 全稱 | 評估面向 | 單位 / 方向 | 筆記連結 |
| :--- | :--- | :--- | :---: | :---: |
| [[SyncNet]] | Sync Confidence Score | 渲染影像唇部動作與音訊的聽視覺同步置信度 | 標量 (↑) | [[SyncNet]] |
| [[LMD]] | Landmark Distance | 生成人臉與真實人臉特徵點幾何距離 | 像素/單位距離 (↓) | [[LMD]] |
| [[Accemo]] | Emotion Accuracy | 第三方預訓練表情辨識網路的情感分類準確率 | % (↑) | [[Accemo]] |
| [[LVE]] | Lip Vertex Error | 唇部幾何對齊與嘴型同步精確度 (最大與平均) | mm / $\times 10^{-4}$ mm / $\times 10^{-5}$ (↓) | [[LVE]] |
| [[FVE]] | Face Vertex Error | 全臉三維網格整體幾何形變精確度 | mm / $\times 10^{-4}$ mm / $\times 10^{-5}$ (↓) | [[FVE]] |
| [[EVE]] | Emotional Vertex Error | 眉眼額頭等上半臉情感表情區域精確度 | $\times 10^{-5}$ / $\times 10^{-6}$ (↓) | [[EVE]] |
| [[EME]] | Emotional Mean Error | 去除嘴唇後情緒關鍵點時間平均 L2 誤差 (TVCG 2024 提出) | $\times 10^{-6}$ mm (↓) | [[EME]] |
| [[FDD]] | Facial Dynamics Deviation | 臉部時序速度與加速度動態自然度 | mm / $\times 10^{-5}$ mm / $\times 10^{-7}$ (↓) | [[FDD]] |
| [[BA]] | Beat Alignment | 頭部動作轉折點與語音聲學節奏對齊一致性 | $[0, 1]$ (↑) | [[BA]] |

---

## 7. 專題綜述與橫向對比 (Syntheses)

| 建立日期 | 專題標題 | 涵蓋論文 / 主題 | 筆記連結 | 可視化呈現 |
| :---: | :--- | :--- | :---: | :---: |
| 2026-11-23 | 3D 臉部動畫前沿綜述與投影片簡報 (2025-2026) | 涵蓋四大模型全維度對比、技術派系分化與實證洞察 | [[2026-11-23_3D_Facial_Animation_Overview]] | [HTML 簡報](file:///c:/Users/user/Downloads/論文22/presentations/3D_Facial_Animation_Survey.html) |
| 2026-11-23 | 3D 臉部動畫技術選型與前沿架構 · 圖文講義指南 | 擬推薦架構、場景決策樹、四大模型深度圖解與避坑指南 | [[2026-11-23_3D_Facial_Animation_Overview]] | [圖文講義手冊](file:///c:/Users/user/Downloads/論文22/handouts/3D_Facial_Animation_Handbook.html) |
| 2026-11-23 | 3D 臉部動畫前沿知識拓撲與圖譜投影片 (Graph Deck) | 全域 15 節點互動知識圖譜、卡片雙軌巡航、關聯跳轉邊 | [[2026-11-23_3D_Facial_Animation_Overview]] | [知識圖譜投影片](file:///c:/Users/user/Downloads/論文22/presentations/3D_Facial_Animation_Graph_Deck.html) |
