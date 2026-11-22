# FLAME (Faces Learned with an Articulated Model and Expressions)

## 1. 概述與定義
**FLAME** (Li et al., ACM TOG / SIGGRAPH Asia 2017) 是當前電腦視覺與 3D 臉部動畫研究中最具代表性的統計三維人臉參數化模型。它結合了線性形狀基底與關節式下顎旋轉（Articulated Jaw）、線性表情基底（Expression Blendshapes）以及線性姿勢校正變形（Pose-dependent Blendshapes）。

FLAME 與 SMPL 身體人體模型同源，具有嚴格定義的拓撲結構與 5023 個 3D 頂點，是當前 3D Talking Head 研究的通用標準拓撲（如 VOCASET, FaceFormer, CodeTalker, RefFace 等均採用此拓撲）。

## 2. 核心技術細節 / 數學結構
- **頂點數量**: 稠密網格（Dense Mesh）包含 $V = 5023$ 個頂點與 9976 個多邊形面。
- **數學公式**:
  $$M(\vec{\beta}, \vec{\theta}, \vec{\psi}) = W(T_P(\vec{\beta}, \vec{\theta}, \vec{\psi}), \mathbf{J}(\vec{\beta}), \vec{\theta}, \mathcal{W})$$
  - $\vec{\beta} \in \mathbb{R}^{|\beta|}$：形狀參數（Shape coefficients，控制身份臉型骨骼）。
  - $\vec{\psi} \in \mathbb{R}^{|\psi|}$：表情參數（Expression coefficients，通常為 50~100 維）。
  - $\vec{\theta} \in \mathbb{R}^{3k+3}$：關節旋轉姿勢（Pose parameters，包括頸部與頭部旋轉 $\rho^{1:3}$、下顎關節 Jaw $\rho^{4:6}$ 與眼球旋轉）。
- **混合運動參數化 (Hybrid Characterization)**:
  在 [[2026_EmoPoseFace]] 中，為同時兼顧面部非剛體形變的高精度捕捉與頭部剛體運動，提出了混合表徵：
  $$X_0^{1:N} = (M_0^{1:N}, B_0^{1:N})$$
  - $M_0 \in \mathbb{R}^{N \times 15069}$：全域 3D Mesh 頂點形變。
  - $B_0 = \rho^{1:3} \in \mathbb{R}^{N \times 3}$：頭部與頸部全局旋轉參數。

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
  - **標準統一的拓撲結構**: 5023 個頂點在所有說話人之間具備嚴格的語義對齊，便於計算頂點級損失（如 LVE、FVE）。
  - **身份與表情完全解耦**: 可透過固定 $\vec{\beta}$、僅預測動態 $\vec{\psi}$ 與下顎旋轉，輕鬆達成跨身份臉部驅動。
- **缺點 / 挑戰**:
  - 直接預測全拓撲頂點座標（$5023 \times 3 = 15069$ 維）計算量遠大於預測 52 維 Blendshapes。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | EmoPoseFace | 混合表徵：5023 頂點 Mesh 形變 + $\rho^{1:3}$ 頭部姿態旋轉 | [[2026_EmoPoseFace]] |
| 2025 | RefFace | 採用 FLAME 拓撲與其 48 個上半臉特徵點作為條件引導 | [[2025_RefFace]] |
| 2025 | DiffusionTalker | 透過 bs2FLAME 映射至 FLAME 拓撲評估頂點誤差 | [[2025_DiffusionTalker]] |
| 2024 | LG-LDM | 輸出 FLAME 5023 頂點 Mesh，解碼以情緒為中心的離散碼本 | [[2024_LGLDM]] |
| 2023 | CodeTalker | 在 FLAME 拓撲上構建離散動作先驗 Codebook | [[CodeTalker]] |
| 2022 | FaceFormer | 在 FLAME 5023 頂點空間上進行自回歸 Transformer 預測 | [[FaceFormer]] |
| 2019 | VOCA | 首次在大規模語音驅動動畫中採用 FLAME 模型 | [[VOCA]] |
