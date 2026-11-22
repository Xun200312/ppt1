# [2024] LG-LDM: Expressive 3D Facial Animation Generation via Local-to-Global Latent Diffusion

- **Title**: Expressive 3D Facial Animation Generation via Local-to-Global Latent Diffusion
- **Authors**: Wenfeng Song, Xuan Wang, Yiming Jiang, Shuai Li*, Aimin Hao, Xia Hou, Hong Qin
- **Venue**: IEEE Transactions on Visualization and Computer Graphics (TVCG), Vol. 30, No. 11, November 2024 (Published Sept 2024)
- **Year**: 2024
- **DOI**: [10.1109/TVCG.2024.3456213](https://doi.org/10.1109/TVCG.2024.3456213)
- **Code**: [GitHub (Face-Diffusion-Model)](https://github.com/wangxuanx/Face-Diffusion-Model)
- **Raw PDF**: [raw/paper/2024_LGLDM.pdf](file:///c:/Users/user/Downloads/論文22/raw/paper/2024_LGLDM.pdf)
- **Input Modality**: [[HuBERT]] (`hubert-large-ls960-ft`), 離散情感標籤 $E_k$ (8 類), 擴散時間步 $t$
- **Output Representation**: [[FLAME]] (5023 頂點 3D 人臉 Mesh)
- **Primary Method**: [[Diffusion_Models]] (局部到全局潛在擴散 LG-LDM) + [[VQ_VAE]] (以情感為中心的 EVQ-VAE)

---

## 1. 一句話核心貢獻 (One-line Takeaway)
> 提出首個將「以情感為中心的離散碼本（EVQ-VAE）」與「局部到全局潛在擴散模型（LG-LDM / FDM）」深層融合的雙階段架構，利用交叉注意力機制精準協同局部嘴唇對齊與全局情緒微表情，攻克語音驅動 3D 臉部動畫中情緒細膩度與音唇同步相互干擾的痛點。

---

## 2. 解決的研究問題 (Problem Formulation)
- **痛點 1 - 情緒與音唇同步的空間衝突 (Spatial-Semantic Conflict)**:
  - 相同的語音輸入在不同情緒語境下具有截然不同的臉部表情（例如憤怒時咬牙切齒 vs. 悲傷時嘴角下垂）。傳統模型若過度關注嘴型，會導致上半臉表情僵硬「石雕臉」；若過度強調情緒，又會導致發音閉唇音（/p/, /b/, /m/）嚴重漏風、音唇不同步。
- **痛點 2 - 傳統 VQ-VAE 碼本難以表徵細膩情感微差異**:
  - 經典離散模型（如 CodeTalker）使用單一無結構碼本對臉部動作進行聚類，缺乏對情感空間的顯式劃分，導致少見情緒（如鄙視、恐懼）在潛在空間中被頻繁出現的中性表情淹沒。
- **核心出發點**:
  - 構建兩階段網絡：第一階段建立**以情緒為中心的離散碼本 (EVQ-VAE)**，將各情緒表情限制在獨立正交的編碼空間；第二階段設計**臉部去噪模型 (FDM)**，透過自注意力提取全局情緒、交叉注意力將語音聲學特徵注入局部唇部，實現「局部唇部對齊」與「全局表情動態」的完美協同。

---

## 3. 核心方法與技術架構 (Methodology)

### 3.1 整體管線 (Two-Stage Pipeline)
```text
【階段 I：以情緒為中心的離散先驗學習 (EVQ-VAE)】
3D 臉部動作序列 M_{1:n} ──> [Transformer 編碼器 E_ec(·, E_k)] ──> 潛在編碼 l
                                                                    │
                                       ┌────────────────────────────┘
                                       ▼
                     [情緒專屬獨立碼本空間 c_k ∈ C] ──> 量化編碼 \tilde{l}
                                                            │
                                                            ▼
                                           [Transformer 解碼器 Dec(·)] ──> 重建 3D Mesh \hat{M}_{1:n}

【階段 II：局部到全局潛在擴散生成 (LG-LDM)】
高斯噪聲 z_T ~ N(0, I) ───┐
                          ▼
語音訊號 A_{1:n} ──> [HuBERT] ──> F_g ──┐
                                       ├──> [臉部去噪模型 FDM] ──> 逐步去噪 ──> z_0 ──> [Dec(·)] ──> 最終 3D 動畫
情緒標籤 E_k + 時間步 t ────────> F_n ──┘
```

### 3.2 關鍵模組
1. **以情緒為中心的 VQ-VAE (EVQ-VAE)**:
   - 傳統 VQ-VAE 採用單一共享碼本，EVQ-VAE 為每種情感 $E_k$ 配置專屬的離散碼本空間 $c_k \in \mathcal{C}$。
   - 確保不同表情的運動元素被限制在特定的正交空間，互不重疊，最大化保留細微微表情。
2. **臉部去噪模型 (Facial Denoising Model, FDM)**:
   - 接收 4 個條件輸入：情緒標籤 $E_k$、當前去噪潛在特徵 $z_t$、微調 HuBERT 聲學特徵 $A_{1:n}$、時間步長 $t$。
   - **全局情緒特徵 $F_n$**：利用多層感知器 (MLP) 與線性層融合 $z_t$ 與 $E_k$，並通過**自注意力 (Self-Attention)** 捕捉全臉整體微表情先驗：
     $$F_n = f(\text{LN}(E_k), \text{MLP}(z_t)), \quad Q_n, K_n, V_n = \text{Linear}(F_n)$$
   - **音訊驅動局部特徵 $F_g$**：融合語音特徵與時間步：
     $$F_g = f(\text{LN}(t), \text{MLP}(A_{1:n}))$$
   - **局部-全局交叉注意力 (Local-Global Cross-Attention)**：將音訊引導特徵作為 Query，與全局表情特徵進行交叉注意力計算：
     $$z_{t-1} = \text{MLP}\left(\text{CA}(Q_g, K_n, V_n), \text{SA}(Q_n, K_n, V_n)\right)$$
   - 該設計確保嘴部區域優先服從語音對嘴，而臉頰、眉心、額頭等非言語區域忠實服從全域情緒。

### 3.3 損失函數 (Loss Functions)
1. **EVQ-VAE 階段損失**:
   $$\mathcal{L}_{evq-vae} = \|M_{1:n} - \hat{M}_{1:n}\|_1 + \|\text{sg}(l) - \tilde{l}\|_2^2 + \beta \|l - \text{sg}(\tilde{l})\|_2^2$$
   其中 $\text{sg}(\cdot)$ 為停止梯度算子，$\beta$ 為平衡潛在空間與離散碼本承諾的權重。
2. **FDM 擴散去噪損失**:
   $$\mathcal{L}_{FDM} = \mathbb{E}_{t \in [1, T]} \left[ \| M_{1:n} - \mathcal{F}(x_t, t, \text{cond}) \|_2^2 \right]$$
   條件項 $\text{cond} = \{ A_{1:n}, E_k \}$。

---

## 4. 實驗設定與評測表現 (Experiments & Benchmarks)

### 4.1 資料集
- **3D MEAD**: 透過 EMOCA 將 2D MEAD 視訊重建為 3D FLAME 網格，採用 Level 3 中 M003~M040 數據，劃分為 Train / Val / Test。
- **RAVDESS**: 廣播級情感語音與歌曲資料庫，用於驗證零樣本泛化。
- **BIWI / VOCASET**: 評估非情緒中性條件下的發音準確度。

### 4.2 核心指標定義
- **LVE (Lip Vertex Error)**: 唇部頂點最大與平均誤差（mm）↓。
- **EME (Emotional Mean Error - 本文新提出)**: 去除嘴唇後、僅計算表情相關關鍵點在時間維度的平均 L2 誤差，專門衡量模型傳遞情感的保真度 ↓。
- **FVE (Face Vertex Error)**: 全臉幾何頂點最大誤差 ↓。
- **FDD (Facial Dynamics Deviation)**: 頂點加速度動態偏差 ↓。

### 4.3 主要量化評測對比
- **3D MEAD 情感測試集對比**:
  | 方法 (Method) | LVE ($10^{-4}$ mm) ↓ | EME ($10^{-6}$ mm) ↓ | FVE ($10^{-7}$ mm) ↓ |
  | :--- | :---: | :---: | :---: |
  | FaceFormer-EMO (2022) | 2.6694 | 3.1015 | 2.6740 |
  | EMOTE (2022) | 2.8883 | 7.3971 | 2.9097 |
  | **LG-LDM (本文)** | **2.0211** | **2.5885** | **2.0144** |

- **BIWI-Test-A 中性發音對比**:
  | 方法 (Method) | LVE ($10^{-4}$ mm) ↓ | FDD ($10^{-5}$ mm) ↓ |
  | :--- | :---: | :---: |
  | FaceFormer (2021) | 5.8514 | 4.7605 |
  | CodeTalker (2023) | 5.1147 | 4.8876 |
  | FaceDiffuser (2023) | **4.7332** | 4.1036 |
  | **LG-LDM (本文)** | 4.9869 | **4.3637** |

- **特定情緒突破 (驚訝/憤怒)**: 在 MEAD 上的消融實驗中，針對「驚訝 (Surprise)」表情，LG-LDM 的情緒誤差相較 EMOTE 改進幅度高達 **5.3588**。
- **用戶偏好研究 (A/B Test)**: 在 3D MEAD 唇部同步上獲得 **62.02%** 的用戶偏好；在情感表達上獲得 **55.75%** 偏好，遠超 FaceFormer-EMO 與 EMOTE。

---

## 5. 優勢與局限性 (Strengths & Limitations)

### 優勢
1. **局部唇動與全臉情緒解耦**: 創新的 FDM 交叉注意力機制避免了強表情對發音口型的拉扯破壞。
2. **情緒空間清晰劃分**: EVQ-VAE 解決了多類情緒微表情在單一碼本中混雜塌陷的歷史難題。
3. **高動態自然度**: 在 FDD 上表面動態偏差上表現優異，表情平滑無抽搐跳幀。

### 局限與未解決問題
1. **依賴 2D 偽真值重建**: 3D MEAD 是利用 EMOCA 從 2D 視訊重構而來，重建誤差會直接引入訓練集。
2. **需要顯式手動指定情感標籤**: 無法端到端直接從說話人的聲調韻律（Prosody）中自適應推論情緒。
3. **去噪採樣延遲**: DDPM 逆過程需要 1000 步迭代，在未經蒸餾的情況下無法直接部署至行動端/VR 即時管線。

---

## 6. 關聯脈絡 (Relations)
- **啟發自 (Builds upon)**: 
  - [[Diffusion_Models]] (LDMs 潛在擴散模型)
  - [[VQ_VAE]] (CodeTalker 離散碼本概念)
  - [[HuBERT]] (自監督語音特徵)
- **被後續改進 / 演進關係 (Extended by)**:
  - [[2025_DiffusionTalker]]: 借鑒擴散模型在臉部動畫中的強大擬真度，進一步研發步數折半蒸餾（1000步 $\to$ 3步），解決了 LG-LDM 採樣耗時的缺陷。
  - [[2026_EmoPoseFace]]: 在情緒解耦基礎上，進一步將「頭部姿態 (Head Pose)」作為獨立分支納入協同生成。
- **競爭比較 (Compared with)**:
  - [[EMOTE]] (2022)
  - [[CodeTalker]] (2023)
  - [[FaceDiffuser]] (2023)
