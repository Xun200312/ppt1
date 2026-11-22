# 3D Facial Animation LLM Wiki & Slide Decks

本專案是一個專注於 **3D 臉部動畫（3D Facial Animation / Talking Head / 3D Facial Avatar Generation）** 的累積型學術知識庫，架構參考 Andrej Karpathy 的 LLM Wiki 模式。

包含五大頂刊/頂會論文的深度解讀、可互動的知識圖譜投影片（Graph Deck）、擬推薦圖文講義指南（Handbook）與經典演講簡報（Survey Deck）。

---

## 🌐 快速線上體驗 (Live Portals)

若已啟用 GitHub Pages，可直接透過瀏覽器訪問：

- **[全域門戶首頁 (Portal)]**(index.html)
- **[知識圖譜投影片 (Graph Deck)]**(presentations/3D_Facial_Animation_Graph_Deck.html)：全域 15 節點動態畫布，可縮放拖曳、卡片展開與關聯邊躍遷
- **[圖文講義推薦指南 (Handbook)]**(handouts/3D_Facial_Animation_Handbook.html)：實戰落地決策樹、星級推薦、7 大維度選型大矩陣與避坑指南
- **[前沿綜述簡報 (Survey Deck)]**(presentations/3D_Facial_Animation_Survey.html)：15 頁 16:9 Y2K Cyber 風格演講投影片，支援 9 大篇章快速跳轉
- **[單篇論文專屬簡報庫]**(presentations/papers/)：
  - [2024 LG-LDM (IEEE TVCG 2024)](presentations/papers/2024_LGLDM.html)
  - [2025 DiffusionTalker (IEEE ICME 2025)](presentations/papers/2025_DiffusionTalker.html)
  - [2025 RefFace (AIAHPC 2025)](presentations/papers/2025_RefFace.html)
  - [2026 EmoPoseFace (IEEE TVCG 2026)](presentations/papers/2026_EmoPoseFace.html)
  - [2026 ESGaussianFace (IEEE TVCG 2026)](presentations/papers/2026_ESGaussianFace.html)

---

## 📂 專案目錄結構 (Directory Layout)

```text
/
├── index.html               # 根目錄首頁導航門戶 (GitHub Pages 入口)
├── README.md                # 專案介紹與使用說明
├── AGENTS.md                # 知識庫維護與 Agent 指引規範
├── presentations/           # 投影片展示目錄 (HTML5 互動網頁)
│   ├── 3D_Facial_Animation_Graph_Deck.html  # 旗艦版知識圖譜投影片
│   ├── 3D_Facial_Animation_Survey.html      # 經典 15 頁分篇簡報
│   └── papers/                              # 5 篇單篇獨立專屬簡報
├── handouts/                # 圖文講義與選型推薦手冊
│   └── 3D_Facial_Animation_Handbook.html    # 擬推薦決策與選型手冊
├── wiki/                    # 學術知識庫 Markdown 筆記 (Karpathy 模式)
│   ├── papers/              # 5 篇論文詳細結構化筆記
│   ├── representations/     # 3D 幾何表徵 (FLAME, Blendshapes, 3DGS 等)
│   ├── methods/             # 核心演算法 (Diffusion, VQ-VAE, Distillation 等)
│   ├── modalities/          # 輸入模態 (HuBERT, Wav2Vec2 等)
│   ├── datasets/            # 資料集評測 (MEAD, BIWI, VOCASET 等)
│   ├── metrics/             # 評測指標 (LVE, EME, FVE, SyncNet 等)
│   ├── syntheses/           # 橫向對比與綜述整理
│   ├── index.md             # 知識庫總索引
│   └── log.md               # 操作日誌
└── raw/                     # 原始論文 PDF 存放目錄 (唯讀依據)
    └── paper/               # 5 篇論文原始 PDF
```

---

## 🚀 如何上傳與部署至 GitHub

### 1. 初始化並推送至 GitHub
```bash
git init
git add .
git commit -m "feat: Initial commit for 3D Facial Animation Wiki and Portals"
git branch -M main
git remote add origin https://github.com/<你的用戶名>/<你的倉庫名稱>.git
git push -u origin main
```

### 2. 開啟 GitHub Pages 網頁託管
1. 進入 GitHub 倉庫頁面，點擊 **Settings** (設定)。
2. 在左側選單點擊 **Pages**。
3. 在 **Branch** 選擇 `main`，資料夾選擇 `/ (root)`，點擊 **Save**。
4. 等待 1~2 分鐘，即可獲得公開訪問的專屬網站網址！
