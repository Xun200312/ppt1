# Triplane (三平面正交空間表徵)

## 1. 概述與定義
**三平面表徵 (Tri-plane Representation)** (Chan et al., CVPR 2022 EG3D) 是一種兼具稠密 3D 體素 (Voxel Grid) 與純隱式神經網路 (MLP) 優點的高效空間表徵架構。

純 3D 體素在儲存與計算上隨解析度呈現 $O(N^3)$ 的立方級暴增，而純 MLP 取樣則計算緩慢。Triplane 將 3D 空間壓縮投影為三個相互垂直的正交 2D 特徵平面（$XY, YZ, XZ$ 平面），使參數量大幅下降至 $3 \times O(N^2)$，同時保留豐富的連續 3D 空間結構先驗。

## 2. 核心技術細節 / 幾何計算
- **結構組成**:
  由三個正交平面張量構成：$T_c = \{F_{xy}, F_{yz}, F_{xz}\}$，每個平面維度為 $R \times R \times C$（$R$ 為空間解析度，$C$ 為特徵通道數）。
- **特徵取樣 (Feature Query)**:
  對於 3D 空間中任意點的座標 $\mu = (x, y, z) \in \mathbb{R}^3$，將其分別正交投影至三個平面，並使用雙線性插值（Bilinear Interpolation）進行取樣：
  $$f_{xy} = \text{Interp}(F_{xy}, (x, y)), \quad f_{yz} = \text{Interp}(F_{yz}, (y, z)), \quad f_{xz} = \text{Interp}(F_{xz}, (x, z))$$
  最終特徵為三者拼接或相加：$f_p = [f_{xy}, f_{yz}, f_{xz}]$。
- **解碼為 3D 高斯點**:
  在 [[2026_ESGaussianFace]] 與 GaussianTalker 中，取樣出的空間特徵 $f_p$ 輸入三平面解碼器 $D_c$，直接回歸出該點的中性高斯參數 $G_{can} = \{\mu, s, q, sh, \alpha\}$。

## 3. 在 3D 臉部動畫與神經渲染中的優缺點
- **優點**:
  - **計算輕量**: 2D 卷積神經網路能直接在 Triplane 上進行特徵提取與上採樣，完美相容現代神經網路加速庫。
  - **高保真細節空間約束**: 有效約束 3D 高斯點在頭部幾何表面上的分佈，防止點雲浮動或漂移。
- **缺點 / 挑戰**:
  - 由於正交投影特性，在特定對角線盲區可能存在表徵奇異點或微小偽影。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | ESGaussianFace | 多解析度三平面編碼規範人臉高斯參數，並引導 ESAM 空間注意力 | [[2026_ESGaussianFace]] |
| 2024 | GaussianTalker | 採用多解析度三平面結合空間-音訊注意力生成 3DGS 談話頭像 | [[GaussianTalker]] |
| 2022 | EG3D | 首次提出 Triplane 架構用於 3D 感知生成對抗網路 | [[EG3D]] |
