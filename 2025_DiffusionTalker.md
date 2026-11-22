# [2025] DiffusionTalker: Efficient and Compact Speech-Driven 3D Talking Head via Personalizer-Guided Distillation

- **Title**: DiffusionTalker: Efficient and Compact Speech-Driven 3D Talking Head via Personalizer-Guided Distillation
- **Authors**: Peng Chen, Xiaobao Wei, Ming Lu, Hui Chen, Feng Tian
- **Venue**: IEEE International Conference on Multimedia and Expo (ICME 2025)
- **Year**: 2025
- **Code**: [GitHub Repository](https://github.com/ChenVoid/DiffusionTalker)
- **DOI**: 10.1109/ICME59968.2025.11208984
- **Raw PDF**: [[raw/paper/2025_DiffusionTalker.pdf]]
- **Input Modality**: [[HuBERT]] (教師模型) / 小型音訊編碼器 (學生模型, CNN + BiLSTM), 語音驅動自適應身份與情緒特徵
- **Output Representation**: [[Blendshapes]] (基於 EmoTalk 的 bs2FLAME 拓撲對齊)
- **Primary Method**: [[Diffusion_Models]] (DDPM + GRU Backbone), [[Knowledge_Distillation]] (步數漸進蒸餾 + 音訊編碼器蒸餾), 對比學習個人化引導

---

## 1. 一句話核心貢獻 (One-line Takeaway)
> 提出首個結合「個人化特徵（身份+情緒）」與「漸進式步數/模型雙重蒸餾」的 3D 臉部動畫擴散模型，將推論步數壓至 2 步（速度提升逾 8 倍達 3632 FPS），模型體積縮減 86.4% 至 45MB，同時兼顧高精度嘴型與生動表情。

---

## 2. 解決的研究問題 (Problem Formulation)
- **現存痛點**:
  1. **推論延遲過高 (Slow Inference)**：傳統擴散模型（如 [[FaceDiffuser]]）雖然解決了自回歸模型的均值回歸與多樣性缺失問題，但需要數十至上千步的反向去噪過程（FPS 僅約 10.33），無法應用於 VR/AR 及行動裝置即時互動。
  2. **模型體積龐大 (Heavy Storage Burden)**：現有方法高度依賴巨型自監督語音特徵抽取器（如 [[HuBERT]], [[Wav2Vec2]]），導致模型體積通常高達數百 MB 甚至數 GB（如 EmoTalk 達 2411MB、SelfTalk 達 1715MB）。
  3. **身份與情感解耦與個人化表達不足 (Lack of Personalization)**：現有方法（如 EmoTalk）多仰賴人為指定的 One-hot 標籤，無法自適應捕捉「不同說話人在相同情緒下獨特的說話風格與微表情」。
- **核心出發點**:
  - 藉助對比學習 (Contrastive Learning) 從輸入語音中自動推斷「說話人身份 (Identity)」與「情感 (Emotion)」特徵，並透過交叉注意力融合為個人化嵌入 (Personalized Embedding)。
  - 透過「個人化引導蒸餾 (Personalizer-Guided Distillation)」，同時完成去噪步數減半壓縮（$16 \to 8 \to 4 \to 2$ 步）與語音編碼器小型化（Large HuBERT $\to$ Small CNN+BiLSTM）。

---

## 3. 核心方法與技術架構 (Methodology)

### 3.1 整體管線 (Pipeline)
系統由兩大核心構成：
1. **Contrastive Personalizer（對比個人化模組）**：
   - 建立 Identity Library 與 Emotion Library。
   - 訓練階段利用監督對比損失使語音特徵 $F_a$ 與對應的身份特徵 $F_{id}$ 及情緒特徵 $F_e$ 對齊。
   - 透過 Cross-Attention 機制（以身份為 Query，情緒為 Key/Value）融合為單一的個人化特徵向量 $p$：
     $$p = \text{softmax}\left(\frac{i \cdot f_k(e)^T}{\sqrt{d_k}}\right) \cdot f_v(e)$$
   - **推論階段無需標籤**：可直接由語音自適應推斷最相近的身份與情緒嵌入。
2. **Motion Decoder（動作解碼器）**：
   - 以三層 GRU 作為主幹，輸入拼接 $[p, F_a, x_t, t]$，經 LayerNorm 與 MLP 逐級去噪預測 3D 臉部動畫動作。

### 3.2 個人化引導蒸餾 (Personalizer-Guided Distillation)
為實現極致的輕量與即時性，採取三維度蒸餾：
1. **步數漸進蒸餾 (Progressive Step Distillation)**：
   - 將 $N$ 步教師模型的相鄰兩步（$2\tau$ 與 $2\tau - 1$）去噪目標結合成學生模型單步 $\tau$ 的監督訊號 $\hat{x}^{te}$。
   - 步數蒸餾損失 $L_{dis} = \max\left(\frac{\alpha_\tau^2}{\sigma_\tau^2}, 1\right) \|\hat{x}^{te} - \hat{x}^{st}\|^2$。
2. **音訊編碼器蒸餾 (Audio Encoder Compression)**：
   - 教師模型採用凍結的完整 HuBERT 提取 $F_a^L$。
   - 學生模型採用輕量化架構（HuBERT 前端 CNN 特徵抽取器 + 雙向 LSTM + 全連接層），透過餘弦相似度損失 $L_{ae} = 1 - \text{mean}\left(\frac{F_a^L}{\|F_a^L\|_2} \cdot \frac{F_a^S}{\|F_a^S\|_2}\right)$ 進行對齊。
3. **個人化增強器 (Personalizer Enhancer)**：
   - 針對教師與學生模型預測結果的**上半臉區域 (Upper Facial Areas, $\hat{x}_u$)**，利用 Cross-Attention 與個人化特徵 $p$ 計算特徵圖 $F_{pu}$。
   - 透過個人化對比損失 $L_{pc}$（拉近同身份同情緒樣本、推開不同身份情緒樣本），強化臉部細微情感與說話風格的表現力。

### 3.3 損失函數 (Loss Functions)
- **教師模型損失**:
  $$\mathcal{L}_{tea} = \lambda_1 \mathcal{L}_{rec} + \lambda_2 \mathcal{L}_{con}^{F_{id}-F_a} + \lambda_2 \mathcal{L}_{con}^{F_e-F_a}$$
- **學生模型蒸餾損失**:
  $$\mathcal{L}_{stu} = \mathcal{L}_{tea} + \lambda_3 \mathcal{L}_{dis} + \lambda_4 \mathcal{L}_{ae} + \lambda_5 \mathcal{L}_{pc}$$
  *(權重設定：$\lambda_1=1.0, \lambda_2=0.007, \lambda_3=0.1, \lambda_4=1.0, \lambda_5=0.05$)*

---

## 4. 實驗設定與評測表現 (Experiments & Benchmarks)

### 4.1 訓練與測試資料集
- **訓練集**: [[BEAT]]、[[3D_ETF]] (RAVDESS 3D 標註版)
- **測試集**: [[3D_ETF]] (In-domain 測試)、[[VOCASET]] (Zero-shot 泛化測試)
- **評測指標**: [[LVE]] (嘴唇頂點誤差)、[[EVE]] (情緒頂點誤差)、[[FDD]] (上半臉動態偏差)、FPS (推論影格率)、Size (模型儲存大小)。

### 4.2 主要量化對比 (3D-ETF 測試集)
| Method | EVE ↓ ($\times 10^{-5}$) | LVE ↓ ($\times 10^{-5}$) | FDD ↓ ($\times 10^{-7}$) | FPS ↑ | Size (MB) ↓ |
| :--- | :---: | :---: | :---: | :---: | :---: |
| FaceFormer (CVPR 2022) | 2.487 | 8.656 | 11.909 | 426.01 | 349 |
| EmoTalk (ICCV 2023) | 1.331 | 2.347 | 2.196 | 1063.13 | 2411 |
| FaceDiffuser (2023) | 2.537 | 6.137 | 8.172 | 10.33 | 860 |
| SelfTalk (ACM MM 2023) | 2.206 | 6.796 | 11.622 | 1428.57 | 1715 |
| UniTalker-B (2024) | 1.439 | 1.727 | 3.004 | 68.75 | 348 |
| **Ours-16 (Teacher)** | 1.275 | **1.021** | **2.023** | 440.01 | 334 |
| **Ours-2 (Student)** | **1.097** | 1.375 | 2.177 | **3632.15** | **45** |

### 4.3 Zero-Shot 評測 (VOCASET 測試集)
| Method | LVE ↓ ($\times 10^{-5}$) | FDD ↓ ($\times 10^{-7}$) | Zero-Shot 條件 |
| :--- | :---: | :---: | :---: |
| FaceFormer | 1.170 | 2.493 | ✗ (直接在 VOCA 訓練) |
| FaceDiffuser | 0.973 | 1.754 | ✗ |
| SelfTalk | 0.967 | 1.049 | ✗ |
| UniTalker-B | **0.814** | 1.396 | ✗ |
| **Ours-2** | 0.857 | **1.198** | **✓ (未見資料集零樣本遷移)** |

### 4.4 消融實驗關鍵發現 (Ablation Insights on 3D-ETF)
- **去掉情緒嵌入 (w/o emotion embedding)**：EVE 從 1.097 惡化至 1.968，顯示情緒編碼對眉眼與額頭表情的決定性作用。
- **去掉增強器 (w/o enhancer)**：EVE 從 1.097 惡化至 1.712，證明聚焦上半臉的對比增強大幅強化了細部情感表達；但增強器使模型重心移至上半臉，導致 LVE 輕微增加（1.116 $\to$ 1.375）。
- **去掉蒸餾 (w/o distillation)**：直接訓練 2 步模型在 EVE (1.305)、LVE (1.647)、FDD (2.618) 全面衰退，證明漸進蒸餾對保留擴散先驗至關重要。

---

## 5. 優勢與局限性 (Strengths & Limitations)

### 優勢
1. **超輕量與超高幀率**：2 步推論速度達到 3632 FPS，模型大小僅 45MB，非常適合部署於 XR 頭戴裝置與行動端。
2. **免標籤的自適應個人化**：推論時僅需輸入一段音訊，即可自主推斷並渲染出說話人特定的身份與情緒風格。
3. **優異的表情自然度 (EVE & FDD)**：透過 Personalizer Enhancer 顯著減少上半臉僵硬問題。

### 局限與未解決問題
1. **上下半臉競爭效應**：聚焦上半臉表情增強時，嘴型區域精準度 (LVE) 略微受損（存在微小 trade-off）。
2. **幾何表徵受限於 Blendshapes**：依賴 bs2FLAME 拓撲轉換，無法直接預測高密度 Dense Mesh 或神經輻射場 (NeRF/3DGS)。

---

## 6. 關聯脈絡 (Relations)
- **啟發自 (Builds upon)**:
  - [[FaceDiffuser]]: 借鑒了擴散模型解決 3D 動畫非確定性映射的思路，但大幅改良其推論速度。
  - [[EmoTalk]]: 借鑒其 bs2FLAME 數據對齊方式，但擺脫其對 One-hot 身分標籤的依賴。
  - Progressive Distillation ([Salimans & Ho, 2022]): 擴散模型漸進式步數折半蒸餾演算法。
- **對比競品 (Direct Competitors)**:
  - [[UniTalker-B]]: 大規模音訊驅動模型（但參數量大且推論較慢）。
  - [[SelfTalk]]: 依賴臉部動作單元 (AU) 的自監督模型（體積達 1.7GB）。
