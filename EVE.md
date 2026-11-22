# EVE (Emotional Vertex Error, 情緒頂點誤差)

## 1. 概述與定義
**EVE (Emotional Vertex Error)** 專門用於衡量 3D 臉部動畫在**情感表情區域（特別是上半臉：眉毛、眼睛、額頭等非言語情感表情區）**的幾何形變精確度。傳統 LVE 指標僅關注唇部對嘴，容易忽略說話人喜怒哀樂的情緒表現，EVE 的引入填補了情感表現力定量評估的空白。

## 2. 核心技術細節 / 數學定義
- **計算區域**: 專注於眼周 (Eyes)、眉毛 (Eyebrows)、額頭 (Forehead) 以及周邊情感表現關鍵區域頂點 $\mathcal{V}_{emo}$。
- **計算公式**:
  $$\text{EVE} = \frac{1}{T} \sum_{t=1}^{T} \max_{v \in \mathcal{V}_{emo}} \|\hat{\mathbf{v}}_t - \mathbf{v}_t^*\|_2$$
- **單位與方向**: 通常以 $\times 10^{-5}$ 或 $\times 10^{-6}$ 標註，**數值越低越好 (↓)**，代表情緒表情幾何還原度越高。

## 3. 在 3D 臉部動畫中的定位
- **消融驗證利器**: 在情感解耦模型中，當移除情緒嵌入或上半臉增強器時，EVE 均會劇烈上升，證明該指標對非語言情感特徵的高度敏感度。

## 4. 代表性基準表現 (Benchmark Reference)

### 3D-ETF 基準
| 模型 | EVE 數值 ($\times 10^{-5}$) ↓ | 說明 |
| :--- | :---: | :--- |
| **DiffusionTalker (Ours-2)** | **1.097** | 表現最優（受惠於 Personalizer Enhancer） |
| **DiffusionTalker (Ours-16)** | 1.275 | 教師模型 |
| EmoTalk | 1.331 | 專門的情緒解耦設計 |
| UniTalker-B | 1.439 | 次之 |
| SelfTalk | 2.206 | 情感偏差較高 |
| FaceFormer | 2.487 | 缺乏情緒模態，表現較差 |

### 3D MEAD-Test 基準
| 模型 | EVE 數值 ($\times 10^{-6}$) ↓ | 說明 |
| :--- | :---: | :--- |
| **EmoPoseFace** | **2.3783** | 雙分支擴散 + ESAU 條件約束，情緒精度最優 |
| EmoVOCA | 2.4302 | 次之 |
| EmoPoseFace (no HP) | 2.4837 | 無頭姿版 |
| LG-LDM | 2.5885 | 局部至全域擴散模型 |
| E-FaceDiffuser | 2.6221 | 帶情緒條件之 FaceDiffuser 微調版 |
| ProbTalk3D | 3.0356 | 離散先驗概率模型 |
| DiffPoseTalk | 3.0127 | 早期頭姿擴散模型 |
| E-FaceFormer | 3.2024 | 自回歸情緒模型 |
| UniTalker | 5.3431 | 統一模型在此指標偏差較大 |
| EMOTE | 6.0703 | 早期情緒解耦模型 |
