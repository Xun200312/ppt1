# Neural Rendering (神經渲染在 3D 臉部動畫的應用)

## 1. 概述與定義
**神經渲染 (Neural Rendering)** 泛指利用深度神經網路將場景的三維幾何與材質表徵轉換為真實二維影像的可微分渲染技術。在 3D 談話頭像領域，研究範式經歷了三大階段的演進：
1. **參數化網格回歸 (Parametric Mesh Regression)**：如 [[FLAME]] 與 [[Blendshapes]]，直接回歸幾何頂點或係數，依賴傳統圖學光柵化（如 PyTorch3D / OpenGL），無法合成複雜毛髮、口腔內部黏膜光澤與皮膚微紋理。
2. **神經輻射場 (Neural Radiance Fields, NeRF)**：利用連續體積渲染合成逼真光影與多視角一致性，但計算繁重（幀率通常 $< 1 \sim 2$ FPS）。
3. **3D 高斯潑濺 (3D Gaussian Splatting, 3DGS)**：顯式點雲結合快速微分光柵化，實現兼顧照片級寫實度與 60+ FPS 的即時渲染。

## 2. 渲染範式橫向對比
| 渲染範式 | 代表模型 | 渲染速度 (FPS) | 多視角 3D 一致性 | 口腔與細節寫實度 | 泛化能力 |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **2D 生成對抗 (GAN / Pixel)** | Wav2Lip, Style2Talker | 15 FPS | 差 (無 3D 幾何約束) | 中等 | 高 (One-shot) |
| **隱式 NeRF 渲染** | AD-NeRF, SyncTalk | 0.1 ~ 1.0 FPS | 極佳 | 極佳 | 低 (需逐人訓練) |
| **顯式 3DGS 渲染** | GaussianTalker, **ESGaussianFace** | **69 ~ 76 FPS** | **極佳** | **極佳** | 低 (需逐人訓練) |

## 3. 核心前沿突破
在 [[2026_ESGaussianFace]] 中，神經渲染技術突破了以往僅能合成單一預設頭像的局限：
- **動態條件光柵化**: 將三維空間高斯點位置偏移 $\Delta \mu$、透明度 $\Delta \alpha$、旋轉 $\Delta q$ 與顏色球諧係數 $\Delta sh$ 同步受控於音訊語音與情緒。
- **神經風格化渲染**: 結合 2D 風格生成器（VToonify）與外在風格損失（$L_{exs}$），直接在 3DGS 輻射場空間預測風格化形變與材質顏色，實現任意藝術頭像（如 3D 卡通、皮克斯風格）的高速渲染。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 渲染基底 | 幀率 | 核心貢獻 | 論文連結 |
| :---: | :--- | :---: | :---: | :--- | :---: |
| 2026 | ESGaussianFace | 3DGS | 69.6 FPS | 首次將 3DGS 神經渲染結合任意情緒調控與藝術風格遷移 | [[2026_ESGaussianFace]] |
| 2024 | SyncTalk | NeRF | 1.03 FPS | 著眼於音訊與視覺的時空對齊同步神經輻射場 | [[SyncTalk]] |
| 2024 | GaussianTalker | 3DGS | 76.8 FPS | 首批 3DGS 實時談話頭像模型 | [[GaussianTalker]] |
| 2021 | AD-NeRF | NeRF | 0.11 FPS | 語音驅動 NeRF 頭部合成開山之作 | [[AD-NeRF]] |
