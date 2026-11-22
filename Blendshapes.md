# Blendshapes (臉部表情混合變形基底)

## 1. 概述與定義
**Blendshapes**（混合變形基底，亦常稱 Morph Targets 或 FACS-based Blendshapes）是電腦動畫與 3D 臉部動畫中最經典且工業化標準最高的參數化表徵。其核心思想是將複雜的臉部表情表示為一組中性臉（Neutral Face）與特定極限表情形變基底（Blendshape Targets）的線性加權組合。常見標準包括蘋果的 **ARKit 52 Blendshapes** 以及基於 FACS（Facial Action Coding System）的動作單元基底。

在 3D 臉部動畫研究中，許多模型亦透過轉換矩陣（如 EmoTalk 提出的 `bs2FLAME`）在 Blendshapes 與 FLAME 拓撲頂點之間相互映射。

## 2. 核心技術細節 / 資料結構
- **數學表示**:
  $$\mathbf{V} = \mathbf{B}_0 + \sum_{k=1}^{K} w_k (\mathbf{B}_k - \mathbf{B}_0) = \mathbf{B}_0 + \mathbf{\Delta B} \cdot \mathbf{w}$$
  其中 $\mathbf{B}_0 \in \mathbb{R}^{N \times 3}$ 為中性網格頂點，$K$ 為基底數量（如 ARKit 為 52），$w_k \in [0, 1]$ 為各個表情基底的激活權重係數。
- **維度特性**: 相比於直接回歸數千個 Mesh 頂點（如 FLAME 5023 頂點），Blendshapes 僅需預測 50~60 維浮點數向量，維度低、計算輕量，可直接被 Unity / Unreal Engine 等遊戲引擎即時渲染。

## 3. 在 3D 臉部動畫中的優缺點
- **優點**:
  - **極致緊湊與低維**: 僅需預測 52 維左右係數，大幅減輕動作解碼器的負擔。
  - **跨引擎相容性**: 工業標準格式，免除頂點重拓撲或 Rigging 綁定成本。
  - **語義明確**: 每個權重直接對應特定臉部肌肉動作（如 `jawOpen`, `eyeBlinkLeft`），便於手動微調與後期修正。
- **缺點 / 挑戰**:
  - **線性疊加限制**: 無法自然表現肌肉交疊擠壓引發的非線性微皺紋或皮膚滑移。
  - **發音人個性化幾何缺失**: 權重本身僅包含表情動作，臉型幾何外觀依賴外部綁定模型。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2025 | DiffusionTalker | 輸出 Blendshapes 係數，並以 bs2FLAME 拓撲計算頂點評測指標 | [[2025_DiffusionTalker]] |
| 2023 | EmoTalk | 提出 bs2FLAME 映射技術解耦表情與身份 | [[EmoTalk]] |
