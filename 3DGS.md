# 3DGS (3D Gaussian Splatting, 3D 高斯潑濺表徵)

## 1. 概述與定義
**3D Gaussian Splatting (3DGS)** (Kerbl et al., SIGGRAPH 2023) 是一種顯式（Explicit）的三維輻射場表徵與渲染技術。傳統的神經輻射場 (NeRF) 採用隱式神經網路（MLP）對連續 3D 空間進行體積光線取樣（Volume Rendering），計算成本高昂且推論極為緩慢（通常僅數幀每秒）。

3DGS 採用大量各向異性（Anisotropic）的顯式 3D 高斯橢球點雲來重構三維幾何與外觀，並配合 GPU 可高度並行處理的自定義微分光柵化器（Differentiable Rasterizer），在達成照片級視覺保真度的同時，實現超過 60~100 FPS 的即時多視角渲染。在 3D 談話頭像領域，3DGS 迅速成為取代 NeRF 的新一代渲染基底。

## 2. 核心技術細節 / 數學結構
- **高斯點參數化**: 每個 3D 高斯點 $G_i$ 包含 5 組核心屬性：
  $$G_i = \{\mu, s, q, sh, \alpha\}$$
  - **中心位置 (Position)**: $\mu \in \mathbb{R}^3$。
  - **尺度縮放 (Scaling)**: $s \in \mathbb{R}^3$，定義協方差橢球三軸半徑。
  - **旋轉四元數 (Rotation)**: $q \in \mathbb{R}^4$，保證正定協方差矩陣 $\Sigma = R S S^T R^T$。
  - **球諧函數 (Spherical Harmonics, SH)**: $sh \in \mathbb{R}^{3(k+1)^2}$，用於表示視角相依的外觀顏色與高光反射。
  - **不透明度 (Opacity)**: $\alpha \in \mathbb{R}$。
- **投影與光柵化 (Splatting Projection)**:
  在相機視角下，3D 高斯投影至 2D 螢幕空間的協方差矩陣為 $\Sigma' = J W \Sigma W^T J^T$（$W$ 為視角變換矩陣，$J$ 為透視投影的 Jacobian 矩陣）。每個像素顏色 $C$ 透過深度排序並依據 $\alpha$-blending 快速累加合成。
- **動態形變機制 (Dynamic Deformations)**:
  在談話頭像任務中，通常先固定一組規範空間高斯點 $G_{can}$（結合三平面 [[Triplane]] 或 3DMM 初始化），再由神經網路（如 GRU 或 MLP）根據音訊與情緒預測時序位移增量：
  $$G(t) = G_{can} + \Delta G(t)$$

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
  - **超高渲染速度 (Real-time FPS)**: 推論幀率通常可達 60~80 FPS（如 ESGaussianFace 達 69.6 FPS），徹底攻克 NeRF 的延遲難題。
  - **嚴格的多視角 3D 一致性**: 徹底消除 2D 談話頭像在視角大角度旋轉時的幾何畸變與邊緣撕裂。
  - **可微分與幾何顯式性**: 能直接結合幾何先驗（如 FLAME 拓撲網格）進行綁定或引導。
- **缺點 / 挑戰**:
  - **通常依賴特定人物訓練 (Person-Specific)**: 大多數 3DGS 談話頭像需要針對特定單一目標人物進行多階段訓練，難以泛化至未見過的任意人臉（Zero-shot 困難）。
  - **記憶體與顯存消耗較大**: 百萬級的高斯點在儲存與顯存傳輸上有一定負擔。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 驅動方式 | 渲染 FPS | 關鍵貢獻 | 論文連結 |
| :---: | :--- | :---: | :---: | :--- | :---: |
| 2026 | ESGaussianFace | 語音 + 情緒 + 藝術風格 | 69.6 FPS | 首次在 3DGS 上實現「語音嘴型 + 連續情緒 + 藝術風格遷移」的統一可控渲染 | [[2026_ESGaussianFace]] |
| 2024 | GaussianTalker | 語音驅動 | 76.8 FPS | 首批採用三平面空間注意力預測 3DGS 形變的談話頭像代表作 | [[GaussianTalker]] |
| 2024 | MonoGaussianAvatar | 單目視頻驅動 | 即時 | 結合 LBS (線性混合蒙皮) 驅動 3D 高斯點雲 | [[MonoGaussianAvatar]] |
