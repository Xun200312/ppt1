# 3D 臉部動畫前沿綜述與分篇投影片簡報 (2025-2026)

- **建立日期**: 2026-11-23
- **涵蓋論文**: [[2025_DiffusionTalker]], [[2025_RefFace]], [[2026_EmoPoseFace]], [[2026_ESGaussianFace]]
- **主簡報 (含分篇快速跳轉條)**: [presentations/3D_Facial_Animation_Survey.html](file:///c:/Users/user/Downloads/論文22/presentations/3D_Facial_Animation_Survey.html)

---

## 1. 簡報導航系統 (Presentation Navigation System)

為方便使用者隨時「分篇跳轉」與「單篇深入」，我們建立了**雙軌簡報系統**：

### 雙軌一：總覽主簡報 (All-in-One Deck with Chapter Navigation)
- **檔案路徑**: [presentations/3D_Facial_Animation_Survey.html](file:///c:/Users/user/Downloads/論文22/presentations/3D_Facial_Animation_Survey.html)
- **特點**: 頂部常駐**篇章快速切換按鈕 (Chapter Jump Pills)**，點擊任一按鈕可瞬間切換到該篇論文的投影片：
  - `[📌 總覽導論]` $\to$ `[📄 01. DiffusionTalker]` $\to$ `[📄 02. RefFace]` $\to$ `[📄 03. EmoPoseFace]` $\to$ `[📄 04. ESGaussianFace]` $\to$ `[📊 05. 綜合對比大表]` $\to$ `[💡 06. 實證洞察]` $\to$ `[🚀 07. 未來趨勢]`

### 雙軌二：分篇獨立專頁 (Standalone Per-Paper Decks)
每篇論文均有獨立的專屬深讀投影片，並支援相互跳轉：
1. **DiffusionTalker 專篇**: [presentations/papers/2025_DiffusionTalker.html](file:///c:/Users/user/Downloads/論文22/presentations/papers/2025_DiffusionTalker.html) (2 步極速蒸餾 / 3632 FPS / 45MB)
2. **RefFace 專篇**: [presentations/papers/2025_RefFace.html](file:///c:/Users/user/Downloads/論文22/presentations/papers/2025_RefFace.html) (48 上半臉特徵點解耦 / 表面法向量損失 / BIWI SOTA)
3. **EmoPoseFace 專篇**: [presentations/papers/2026_EmoPoseFace.html](file:///c:/Users/user/Downloads/論文22/presentations/papers/2026_EmoPoseFace.html) (雙分支頭姿解耦 / GL-FFE 模組 / FMSB 局部肌肉微操)
4. **ESGaussianFace 專篇**: [presentations/papers/2026_ESGaussianFace.html](file:///c:/Users/user/Downloads/論文22/presentations/papers/2026_ESGaussianFace.html) (3DGS 實時神經渲染 / 69.6 FPS / 空間注意力與風格化)

---

## 2. 核心大表：四大模型全維度深度橫向對比

| 模型 (年份) | 輸出表徵空間 | 演算法主幹 | 音訊特徵 | 去噪步數 / 速度 | 模型體積 | 額外控制維度 | 代表性 SOTA 指標 |
| :--- | :--- | :--- | :--- | :---: | :---: | :--- | :--- |
| **[[2025_DiffusionTalker]]**<br>(ICME 2025) | [[Blendshapes]]<br>(52維 low-dim) | DDPM + GRU<br>+ 漸進步數蒸餾 | [[HuBERT]] 蒸餾至<br>CNN+BiLSTM | **2 步 / 3632 FPS** | **45 MB**<br>(-86.4%) | 身份+情緒自適應<br>(對比學習推論免標籤) | 3D-ETF EVE 1.097<br>VOCA Zero-shot LVE 0.857 |
| **[[2025_RefFace]]**<br>(AIAHPC 2025) | 稠密 3D Mesh<br>([[FLAME]] / [[BIWI]]) | DDPM + 空間時間雙注意力 + FiLM | [[HuBERT]] (凍結前段) | 10 步 / 0.36 秒 | 標準權重 | 48 個臉部特徵點<br>+ 表面法向量損失 ($L_{snv}$) | BIWI-Test-A SOTA<br>LVE 3.8466, FDD 2.9857 |
| **[[2026_EmoPoseFace]]**<br>(TVCG 2026) | 混合運動表徵<br>FLAME Mesh + [[Head_Pose]] | 雙分支 GRU 擴散<br>+ [[Facial_Motion_Editing]] | [[HuBERT]]-base<br>(降至 512 維) | 1000 步擴散 | 標準權重 | 頭部姿態同步 + 7類情緒<br>FMSB 局部肌肉 ([[Action_Units]]) 微操 | 3D MEAD SOTA<br>LVE 1.3211, BA 0.33 |
| **[[2026_ESGaussianFace]]**<br>(TVCG 2026) | 3D 高斯輻射場<br>([[3DGS]]) + [[Triplane]] | 微分光柵化 [[Neural_Rendering]]<br>+ ESAM 空間注意力 | [[DeepSpeech]]<br>(時序擴展) | **即時 69.6 FPS** | 逐人 3DGS 點雲 | 多視角一致性 + 任意情緒<br>+ 藝術風格遷移 (VToonify) | MEAD 影像 SOTA<br>PSNR 31.87, FID 16.93 |
