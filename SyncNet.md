# SyncNet (唇形同步置信度指標, Sync Score)

## 1. 概述與定義
**SyncNet** (Chung & Zisserman, ACCV 2016 "Out of Time: Automated Lip Sync in the Wild") 是一種基於雙分支卷積神經網路（分別處理音訊 Mel 頻譜圖與連續唇部圖像影格）的自動對嘴評估與唇讀網路。

在 2D 與可視化 3D 談話頭像生成（Talking Head Generation）中，SyncNet 輸出的**同步置信度 (Sync Confidence Score, Sync)** 與**特徵距離偏差 (LSE-C, LSE-D)** 是衡量生成影片中嘴唇運動與輸入語音是否精準對齊的最重要感知指標。

## 2. 核心技術細節 / 數學定義
- **計算原理**:
  網路輸入一段長度為 5 影格（約 0.2 秒）的面部嘴唇圖像序列與對應的音訊頻譜，將二者映射至共同的嵌入空間，並計算餘弦距離。
- **Sync 置信度**:
  在時序偏移滑動視窗下，找到音視訊特徵距離最小的時間偏移點，若偏移量為 0 且置信度峰值越高，則 Sync 分數越高。
- **單位與方向**: **數值越高越好 (↑)**（例如真人未壓縮影片的 Sync 分數通常在 6~8 左右）。

## 3. 在 3D 臉部動畫與可視化渲染中的定位
- **有別於 LVE**: 幾何指標 [[LVE]] 只能在具有 3D 捕捉 Ground Truth 的網格上計算，而 SyncNet 可直接評估神經渲染（NeRF/3DGS）渲染出的最終 RGB 影片，具備客觀人眼聽視覺感知對齊特性。

## 4. 代表性基準表現 (MEAD 資料集)
| 模型 | 渲染方式 | Sync 得分 ↑ |
| :--- | :--- | :---: |
| **ESGaussianFace** | 3DGS 渲染 | **6.216** |
| Wav2Lip | 2D Pixel (以 SyncNet 為判別器) | 5.851 |
| Style2Talker | 2D 擴散模型 | 5.294 |
| GaussianTalker | 3DGS 渲染 | 5.202 |
| SyncTalk | NeRF 渲染 | 4.770 |
| MakeItTalk | 2D 關鍵點 | 4.495 |
| AD-NeRF | NeRF 渲染 | 1.298 |
