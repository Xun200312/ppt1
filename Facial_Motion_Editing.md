# Facial Motion Editing (3D 臉部動畫編輯技術)

## 1. 概述與定義
**臉部動作編輯 (Facial Motion Editing)** 是指在已經生成的語音驅動 3D 臉部動畫序列基礎上，對特定時間段（Temporal）或特定面部區域（Spatial）進行二次修訂與微調的技術。常見需求包括：手動插入眨眼動作、微調眉毛挑動幅度、切換或加強某一句的情感強度。

## 2. 現有編輯範式對比
| 編輯方法 | 代表作 | 控制維度 | 局限性 |
| :--- | :--- | :--- | :--- |
| **關鍵影格編輯 (Keyframe Editing)** | 3DiFace (2023) | 全域稀疏關鍵影格插值 | 動作過渡生硬，無法針對局部肌肉獨立編輯 |
| **標籤與風格切換** | EMOTE (2023), Media2Face (2024) | 全域文字/圖像風格引導 | 缺乏微觀肌肉層級控制，無法細緻控制單個眨眼或皺眉 |
| **Blendshape 粗粒度編輯** | CafeTalk (2025) | 依賴 ARKit 係數調整 | 局限於 Blendshape 空間，時序過渡缺乏物理連續性 |
| **時空細粒度多階編輯** | **GL-FFE (EmoPoseFace, 2026)** | **AU + ESAU + FMSB 演算法** | **在 3D Mesh 上兼顧自然漸層過渡與嘴型保真** |

## 3. GL-FFE 核心機制與 FMSB 演算法
在 [[2026_EmoPoseFace]] 中，作者提出了 **GL-FFE (Global-local Facial Fine-grained Editing Module)**：
1. **中階全局增強 (Mid-level ESAU)**：
   $$X_k^{1:N} = \hat{X}_0^{1:N} + W_{emo} \Delta ESAU_k$$
   調節係數 $W_{emo} \in [0, 1]$ 實現宏觀情緒強度漸變。
2. **低階局部平滑編輯 (Low-level FMSB Algorithm)**：
   傳統離散關鍵影格容易導致動作跳變。**FMSB (Facial Motion Sequence BlendMorph)** 引入了物理式對稱窗函數：
   - 參數設定：起始關鍵影格 $I$、局部肌肉位移量 $\Delta b$（如 AU7 瞇眼）、持續時間 $\tau$、以及上升/下降過渡窗長度 $w$。
   - 加權過渡函數 $\phi(i; I, w, \tau)$：
     $$\phi(i; I, w, \tau) = \begin{cases} \frac{i-(I-w)}{w+1} & I-w \le i < I \quad (\text{平滑上升激活}) \\ 1 & I \le i < I+\tau \quad (\text{動作保持}) \\ 1 - \frac{i-(I+\tau-1)}{w+1} & I+\tau \le i < I+\tau+w \quad (\text{平滑衰減回復}) \\ 0 & \text{otherwise} \end{cases}$$
   - 最終編輯運動：$M'(i) = M(i) + \phi(i; I, w, \tau) \cdot W_{local} \Delta b$。
   此設計保證了在任意影格插入微表情時，均能具備自然的肌肉彈性伸展與回彈，且完全不影響唇部的語音同步基線。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | EmoPoseFace | 提出 GL-FFE 模組與 FMSB 演算法，實現 3D Mesh 上的細粒度微表情編輯 | [[2026_EmoPoseFace]] |
