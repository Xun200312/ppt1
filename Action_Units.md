# Action Units (臉部動作單元, AU) & ESAU

## 1. 概述與定義
**動作單元 (Action Units, AU)** 源自心理學家 Paul Ekman 等人於 1978 年創立的**臉部動作編碼系統 (Facial Action Coding System, FACS)**。它將人類臉部可解剖獨立分離的面部肌肉收縮解構為數十個具備明確語義的基礎動作單元（例如：AU1 揚眉內側、AU2 揚眉外側、AU4 皺眉、AU12 嘴角上揚等）。

在 3D 臉部動畫中，傳統研究多直接使用混合形變 (Blendshapes) 或端到端頂點回歸。在 [[2026_EmoPoseFace]] 中，作者提出了將 51 個 ARKit Blendshapes 重新對齊並線性重組為 15 個核心 AU，並進一步構造了**情緒專用動作單元 (Emotion-Specific Action Unit, ESAU)**，實現了從低階肌肉微操到高階情緒強度的階層式語義控制。

## 2. 核心技術細節 / 資料結構
- **15 個核心 FACS 動作單元 (AU)**:
  - AU1 (Inner Brow Raiser), AU2 (Outer Brow Raiser), AU4 (Brow Lowerer)
  - AU5 (Upper Lid Raiser), AU6 (Cheek Raiser), AU7 (Lid Tightener)
  - AU9 (Nose Wrinkler), AU12 (Lip Corner Puller), AU14 (Dimpler)
  - AU15 (Lip Corner Depressor), AU16 (Lower Lip Depressor), AU20 (Lip Stretcher)
  - AU23 (Lip Tightener), AU26 (Jaw Drop)
- **ESAU 情感動作單元組合矩陣 (Table I from EmoPoseFace)**:
  | 情感類型 | FACS AU 啟動加權組合 |
  | :--- | :--- |
  | **Happy (高興)** | AU6 (100%) + AU12 (80%) |
  | **Sad (悲傷)** | AU1 + AU4 + AU15 (100%) |
  | **Surprise (驚訝)** | AU1 + AU2 + AU5 + AU26 (100%) |
  | **Fear (恐懼)** | AU1+2+4+5+7 (100%) + AU20+26 (50%) |
  | **Angry (憤怒)** | AU4 + AU5 + AU7 + AU9 + AU23 (100%) |
  | **Disgust (厭惡)** | AU9 (100%) + AU15 (80%) + AU16 (40%) |
  | **Contempt (蔑視)** | AU12 (10%), 單側嘴角上揚 (Unilateral lift 100%) |

## 3. 在 3D 臉部動畫中的定位與應用
- **精準非干涉編輯**: 在發音對話中，若直接調整全臉參數往往會破壞嘴型同步。透過 AU 約制，可刻意降低影響嘴唇形變的 AU 權重，專注微調眼部 (AU5, AU7) 與額頭眉毛 (AU1, AU2, AU4) 的表情。
- **全局強度調節**: 透過權重係數 $W_{emo} \in [0, 1]$ 縮放 $\Delta ESAU_k$，使同一句台詞可無縫渲染從「微怒」到「暴怒」的連續漸層。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | EmoPoseFace | 提出 ESAU 7 類情緒組合與 AU 物理過渡編輯 (GL-FFE) | [[2026_EmoPoseFace]] |
| 2023 | SelfTalk | 引入自監督學習以動作單元 (AU) 輔助嘴唇動作預測 | [[SelfTalk]] |
