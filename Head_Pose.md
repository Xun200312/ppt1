# Head Pose (頭部姿態與律動表徵)

## 1. 概述與定義
**頭部姿態 (Head Pose)** 指頭部相對於軀幹或相機空間的三維剛體旋轉與平移（通常以尤拉角 Euler Angles、旋轉向量 Rodrigues Vector 或四元數 Quaternion 表示，在 FLAME 中通常記為 $\rho^{1:3} \in \mathbb{R}^3$）。

心理學與認知科學研究（Livingstone et al., 2016; Witkower et al., 2022）證實：**頭部姿態並非孤立的附屬動作，而是人類語音節奏 (Speech Rhythm) 與情緒傳達的核心載體**。例如強調重點時的低頭點擊、驚訝時的後仰、困惑時的微幅傾斜。若虛擬數位人僅有嘴巴動作而頭部靜止，會引發極強烈的「恐怖谷效應 (Uncanny Valley)」。

## 2. 核心技術挑戰
1. **剛體旋轉與非剛體肌肉運動的頻率衝突**:
   - 臉部肌肉形變（非剛體，高頻音素對齊）與頭部搖晃（剛體旋轉，低頻宏觀語義律動）在時序動力學上分佈極為不同。
   - 若直接將頭部角度與臉部頂點放在同一個單一網路預測（如單分支 GRU），極易引發表情痙攣或嚴重的頭部逐幀高頻抖動 (Head Jitter)。
2. **抖動平滑 vs 節拍對齊 (Beat Alignment)**:
   - 必須透過時序二階速度與平滑損失約束（如 $\mathcal{L}_{vel}^{head}, \mathcal{L}_{smooth}^{head}$）抑制高頻抖動，同時保持與語音音節重音的一致性（評估指標為 [[BA]]）。

## 3. 解決方案範式演進
- **風格參考影片驅動**: DiffPoseTalk (Sun et al., TOG 2024) 透過提取參考影片中的說話頭部風格引導擴散模型。
- **雙分支解耦擴散 (Dual-Branch Decoupled Diffusion)**:
  [[2026_EmoPoseFace]] 首次提出表情與頭姿並行的雙分支架構，頭部姿態由專屬的 Pose Style Transformer Encoder 提取條件，並配合專用損失獨立收斂，使 BA 達到 0.33，徹底消除了先前面部模型預測頭部時的晃動缺陷。

## 4. 代表性使用論文 (Key Papers)
| 年份 | 論文名稱 | 用法簡述 | 論文連結 |
| :---: | :--- | :--- | :---: |
| 2026 | EmoPoseFace | 雙分支擴散模型解耦臉部與頭部，結合 Pose Style 實現情感與頭姿協同生成 | [[2026_EmoPoseFace]] |
| 2024 | DiffPoseTalk | 使用擴散模型生成語音風格化 3D 臉部與頭部動作 | [[DiffPoseTalk]] |
