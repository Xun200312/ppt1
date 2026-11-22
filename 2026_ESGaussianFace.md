# [2026] ESGaussianFace: Emotional and Stylized Audio-Driven Facial Animation via 3D Gaussian Splatting

- **Title**: ESGaussianFace: Emotional and Stylized Audio-Driven Facial Animation via 3D Gaussian Splatting
- **Authors**: Chuhang Ma, Shuai Tan, Ye Pan, Jiaolong Yang, Xin Tong
- **Venue**: IEEE Transactions on Visualization and Computer Graphics (TVCG), Vol. 32, No. 3, March 2026
- **Year**: 2026
- **Code**: N/A
- **DOI**: 10.1109/TVCG.2026.3651640
- **Raw PDF**: [[raw/paper/2026_ESGaussianFace.pdf]]
- **Input Modality**: [[DeepSpeech]] (語音內容特徵 $a^{1:T}$), 情感參考圖像/影片 (經 3DMM [[Deep3D]] 提取表情係數 $e \in \mathbb{R}^{64}$、AU45 眨眼特徵 $y$、相機視角 $v$), 藝術風格圖像 (經 pSp Style Encoder 提取風格特徵 $s \in \mathbb{R}^{128}$)
- **Output Representation**: [[3DGS]] (3D Gaussian Splatting 顯式點雲輻射場, $G = \{\mu, s, q, sh, \alpha\}$) + [[Triplane]] 三平面正交空間表徵
- **Primary Method**: [[Neural_Rendering]] (3DGS 神經渲染器, 69.6 FPS 即時多視角渲染), 情感-音訊引導空間注意力 (ESAM), [[Multi_Stage_Training]] (中性發音 $\to$ 情感形變 $\to$ 藝術風格遷移三階段訓練)

---

## 1. 一句話核心貢獻 (One-line Takeaway)
> 首次將 3D Gaussian Splatting (3DGS) 擴展至同時支援「語音對嘴 + 任意連續情感控制 + 藝術風格遷移」的多視角可視化談話頭像生成，並透過三階段訓練策略與 ESAM 空間注意力機制，在實現 69.6 FPS 實時渲染的同時徹底解決了 NeRF 渲染延遲與 2D 生成缺乏 3D 一致性的難題。

---

## 2. 解決的研究問題 (Problem Formulation)
- **現存痛點**:
  1. **2D 談話頭像缺乏三維一致性 (Lack of 3D Consistency)**: 早期 2D 像素生成模型（如 Wav2Lip, Style2Talker, DreamTalk）無法支援相機視角旋轉，容易產生面部姿態畸變與邊緣撕裂。
  2. **NeRF 神經輻射場渲染極慢 (NeRF Rendering Bottleneck)**: AD-NeRF、SyncTalk 等基於隱式神經輻射場的方法雖然保證了多視角一致性，但依賴體積光線採樣，推論幀率極低（AD-NeRF 僅 0.11 FPS，SyncTalk 僅 1.03 FPS），無法實時互動。
  3. **3DGS 缺乏情感泛化與風格化控制**: 現有 3DGS 頭像（如 GaussianTalker）大多僅限於中性語音，遇到混合情緒音訊時容易混淆表情，且過去沒有任何 3DGS 模型能同時解耦任意藝術頭像風格與情感。
- **核心出發點**:
  - 採用顯式 3DGS 作為幾何與外觀載體，結合三平面 (Tri-plane) 空間解碼獲得標準面部高斯點；
  - 提出空間注意力模組 ESAM，動態將語音特徵引導至嘴部，將情緒特徵引導至眉眼與額頭；
  - 採用「中性嘴型 $\to$ 情感形變 $\to$ 藝術風格化」三階段遞進式訓練，實現任意單一模型對情緒與風格的平滑線性插值。

---

## 3. 核心方法與技術架構 (Methodology)

### 3.1 三平面 3D 高斯生成器 (Triplane-Based 3D Gaussian Generator)
- 以 3DMM 標準人臉為基礎初始化 $N$ 個 3D 高斯點。
- 建立多解析度正交三平面 $T_c = \{F_{xy}, F_{yz}, F_{xz}\}$。對任意空間點 $\mu \in \mathbb{R}^3$，投影至三平面獲取空間特徵 $f_p = T_c(\mu)$。
- 透過三平面解碼器 $D_c$ 解碼出中性臉的規範 3D 高斯參數：
  $$G_{can} = \{\mu, s, q, sh, \alpha\}$$

### 3.2 情感-音訊引導空間注意力 (ESAM)
為了使音訊與情緒特徵能自適應影響不同的面部空間區域，設計 ESAM 模組：
- **AGA (Audio-guided Attention)**：預測音訊內容特徵對唇部區域的動態影響。
- **EGA (Emotion-guided Attention)**：估計 3DMM 情緒特徵 $e$ 對眼睛、眉毛、臉頰等情緒敏感區域的形變權重。
- 注意力權重熱力圖證實：語音注意力高度聚焦於嘴唇周圍，而情緒注意力均勻且敏感地分佈於眉眼與全臉。

### 3.3 高斯形變預測與風格化 (ESGaussian Deformation)
- **情感形變預測器 $P_{emo}$**：結合空間特徵 $z_B$、情緒向量 $e$ 與 3D 空間位置編碼 $p \in \mathbb{R}^{64}$，預測高斯點偏移量 $\Delta G_{emo}$。
- **風格形變預測器 $P_{sty}$**：進一步引入由 pSp Style Encoder 抽取的藝術風格編碼 $s \in \mathbb{R}^{128}$，預測風格化位移 $\Delta G_{sty}$。
- 神經渲染器 $\mathcal{R}$ 實時光柵化投影輸出 RGB 圖像 $\hat{I} = \mathcal{R}(G_{can} + \Delta G)$。

### 3.4 三階段訓練策略 (Multi-Stage Training Strategy)
1. **第一階段（中性發音與唇動）**:
   - 僅使用說話人的中性語音與影片訓練 $M_G$ 與 $P_{emo}$，損失包含 $L_{rgb}, L_{per}, L_{ssim}$，外加嘴部遮罩 L1 損失 $L_{lip}$、特徵點距離損失 $L_{ld}$ 與時序平滑損失 $L_{smo}$。
2. **第二階段（情感形變學習）**:
   - 凍結 $M_G$ 參數，使用同發音人的多種情感影片進行訓練，在維持嘴型同步的前提下學習面部情感動態。
3. **第三階段（3DGS 風格遷移學習）**:
   - 使用 VToonify 對情感影片進行風格化離線處理作為偽真值，引入外在風格損失 $L_{exs} = \|E_s(I_{sty}) - E_s(\hat{I}_{sty})\|_1$ 訓練風格預測器 $P_{sty}$。

---

## 4. 實驗設定與評測表現 (Experiments & Benchmarks)

### 4.1 訓練與評測設定
- **資料集**: [[MEAD]] (60 位演員, 8 種情緒)、[[RAVDESS]] (24 位演員, 8 種情緒)、藝術風格資料集。
- **比較基準**: 
  - 傳統 2D: MakeItTalk, Wav2Lip, Audio2Head.
  - NeRF / 3DGS 渲染: AD-NeRF, SyncTalk, GaussianTalker.
  - 情感 ADFA: MEAD, EAMM, EAT, DreamTalk, EDTalk, Style2Talker.
- **評測指標**:
  - 影像品質: PSNR ↑, SSIM ↑, FID ↓, LPIPS ↓
  - 嘴型同步: [[SyncNet]] (Sync Confidence) ↑
  - 表情姿態精度: [[LMD]] (Landmark Distance) ↓
  - 情感準確度: [[Accemo]] (情緒分類準確率) ↑
  - 運算幀率: FPS ↑

### 4.2 主要量化結果 (MEAD 與 RAVDESS 基準)
| 資料集 | 方法 | PSNR ↑ | SSIM ↑ | FID ↓ | LPIPS ↓ | Sync ↑ | LMD ↓ | $Acc_{emo}$ ↑ (%) | 多視角支援 | 風格化支援 |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **MEAD** | Wav2Lip | 27.819 | 0.670 | 46.373 | 0.051 | 5.851 | 5.068 | 16.80 | ✗ | ✗ |
| | Style2Talker | 29.455 | 0.795 | 25.282 | 0.035 | 5.294 | 3.007 | 70.27 | ✗ | ✓ |
| | AD-NeRF | 25.282 | 0.549 | 84.506 | 0.086 | 1.298 | 10.245 | 10.71 | ✓ | ✗ |
| | SyncTalk | 28.120 | 0.803 | 32.959 | 0.040 | 4.770 | 5.071 | 48.25 | ✓ | ✗ |
| | GaussianTalker| 28.911 | 0.818 | 22.290 | 0.038 | 5.202 | 4.981 | 52.62 | ✓ | ✗ |
| | **ESGaussianFace** | **31.873**| **0.901**| **16.933**| **0.028**| **6.216**| **2.832** | **75.94** | **✓** | **✓** |
| **RAVDESS**| Wav2Lip | 27.931 | 0.715 | 32.567 | 0.057 | 6.169 | 5.623 | 18.03 | ✗ | ✗ |
| | GaussianTalker| 28.516 | 0.742 | 25.260 | 0.044 | 4.354 | 5.329 | 51.32 | ✓ | ✗ |
| | **ESGaussianFace** | **30.772**| **0.833**| **21.796**| **0.034**| **5.757**| **3.145** | **71.12** | **✓** | **✓** |

### 4.3 效率與推論幀率對比 (FPS)
| 方法 | AD-NeRF | SyncTalk | Wav2Lip | Style2Talker | GaussianTalker | **ESGaussianFace (Ours)** |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **FPS ↑** | 0.112 | 1.030 | 15.243 | 14.905 | **76.802** | **69.624** |

> **數據亮點**: ESGaussianFace 達到了近 70 FPS 的超高即時渲染速度，遠超傳統 NeRF 數百倍，且在多視角與風格化附加模組下，幾乎沒有犧牲 GaussianTalker 的推論速度。

### 4.4 線性連續插值操控 (Linear Interpolation)
透過簡單線性插值 $f = \alpha f_1 + (1-\alpha) f_2$（$\alpha \in [0, 1]$），模型能平滑展示從「喜悅」到「憤怒」的面部漸變，或是從「真人寫實」到「3D 卡通」的風格連續過渡。

---

## 5. 優勢與局限性 (Strengths & Limitations)

### 優勢
1. **全能多維控制**: 唯一同時支持「語音唇形同步 + 任意情感控制 + 藝術風格遷移 + 多視角即時神經渲染」的 3DGS 系統。
2. **極致即時性 (69.6 FPS)**: 具備端到端高保真渲染能力，適合各類 XR/VR 即時數位人場景。
3. **優異的表情與影像指標**: 在 PSNR (31.87), SSIM (0.901), FID (16.93), LMD (2.832), Acc_emo (75.9%) 全面刷新 SOTA。

### 局限性
1. **依賴特定人物訓練 (Person-Specific 3DGS Field)**: 與多數神經輻射場/高斯頭像一致，每換一個新人物需要獨立訓練一組 3D 高斯場，缺乏泛化性 Zero-Shot 遷移能力。
2. **風格訓練依賴 2D 偽真值**: 風格化階段高度依賴 2D 視頻風格化演算法（VToonify）生成的離線資料，可能受限於 2D 工具的瑕疵。

---

## 6. 關聯脈絡 (Relations)
- **代表性技術分支 (3DGS Neural Rendering)**:
  - 標誌著 3D 臉部動畫從純「參數/頂點回歸（如 [[2025_DiffusionTalker]], [[2025_RefFace]], [[2026_EmoPoseFace]]）」向**「端到端可視化神經渲染（Photorealistic 3DGS Rendering）」**的跨越。
  - 對比 [[2026_EmoPoseFace]]：EmoPoseFace 輸出骨骼與網格拓撲（FLAME），而 ESGaussianFace 直接光柵化輸出帶有光影外觀的高畫質 RGB 影像。
- **對比競品**:
  - GaussianTalker: 缺乏情感與風格控制，容易混淆情緒。
  - AD-NeRF & SyncTalk: 渲染速度過慢（$\sim 1$ FPS），無法即時交互。
  - Style2Talker: 僅限 2D 像素空間，缺乏 3D 多視角幾何一致性。
