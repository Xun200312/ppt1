# 3D Facial Animation Wiki Log (操作日誌)

本檔案為時間軸日誌（Append-only），記錄所有 Ingest、Query 沉澱、Lint 健康檢查等重要操作。

---

- [2026-11-23] **GitHub Deployment & Root Portal**: 為支援將整個知識庫與互動投影片發布至 GitHub / GitHub Pages，完成根目錄架構升級：
  - 新增全域門戶首頁 [index.html](file:///c:/Users/user/Downloads/論文22/index.html)，整合知識圖譜投影片、圖文講義、分篇簡報與單篇專題之直達入口。
  - 新增專案說明 [README.md](file:///c:/Users/user/Downloads/論文22/README.md) 與 [.gitignore](file:///c:/Users/user/Downloads/論文22/.gitignore)。
- [2026-11-23] **Ingest**: [[2024_LGLDM]] - 發表於 IEEE TVCG 2024 (Vol. 30, No. 11)。提出以情緒為中心的離散碼本 (EVQ-VAE) 與局部到全局潛在擴散模型 (LG-LDM / FDM)，攻克音唇同步與情緒微表情相互干擾之核心瓶頸，並提出 EME 評測指標。開源工具包 [wangxuanx/Face-Diffusion-Model](https://github.com/wangxuanx/Face-Diffusion-Model)。
- [2026-11-23] **Flagship Interactive Graph Presentation Deck**: 嚴格依循使用者審閱定案之「四階段研發流程」，正式交付知識圖譜互動式投影片：
  - 新增核心旗艦檔案 [presentations/3D_Facial_Animation_Graph_Deck.html](file:///c:/Users/user/Downloads/論文22/presentations/3D_Facial_Animation_Graph_Deck.html)。
  - 核心規格：雙軌視角機制（全域動態知識圖譜畫布 ＋ 15 頁詳細投影片微觀卡片）、五大聚類 15 個核心節點無死胡同互聯、32 條語意有向躍遷邊（Inbound / Outbound Jump Pills）、15 幅專屬高清向量架構圖、KaTeX 數學公式即時渲染與鍵盤導航 (`←`/`→`/`Esc`/`G`)。
  - 全面互聯：與主簡報、講義手冊及各單篇頁面建立多向互通導覽網絡。
- [2026-11-23] **Recommendation & Illustrated Handbook**: 依據使用者需求，產出全套「擬推薦圖文講義手冊」：
  - 新增講義文件 [handouts/3D_Facial_Animation_Handbook.html](file:///c:/Users/user/Downloads/論文22/handouts/3D_Facial_Animation_Handbook.html)。
  - 核心設計：四大實務落地場景推薦指數（⭐⭐⭐⭐⭐）、視覺化推薦決策樹 SVG、四大模型架構流程圖解、深度避坑指針、7 大維度橫向實戰選型大矩陣、聲學編碼器選型定論，以及 3 道自我檢核考題。
  - 雙向打通：主投影片與講義手冊頂部互相設置一鍵直達連結。
- [2026-11-23] **Navigation & Modular Slides Enhancement**: 依據使用者需求，重構並擴充投影片為「分篇跳轉導覽系統」：
  - 更新主簡報 [presentations/3D_Facial_Animation_Survey.html](file:///c:/Users/user/Downloads/論文22/presentations/3D_Facial_Animation_Survey.html)：增加常駐頂部篇章直達條（支援 1 鍵快速跳至任一篇論文或綜合對比大表）。
  - 新建 4 份獨立單篇專屬簡報，放置於 `presentations/papers/`：
    - [[2025_DiffusionTalker] 專篇](file:///c:/Users/user/Downloads/論文22/presentations/papers/2025_DiffusionTalker.html)
    - [[2025_RefFace] 專篇](file:///c:/Users/user/Downloads/論文22/presentations/papers/2025_RefFace.html)
    - [[2026_EmoPoseFace] 專篇](file:///c:/Users/user/Downloads/論文22/presentations/papers/2026_EmoPoseFace.html)
    - [[2026_ESGaussianFace] 專篇](file:///c:/Users/user/Downloads/論文22/presentations/papers/2026_ESGaussianFace.html)
  - 各單篇簡報支援「返回總覽」與「按鈕直接切換下一篇」，雙向連通。
  - 更新綜述頁面 [[2026-11-23_3D_Facial_Animation_Overview]]。
- [2026-11-23] **Synthesis & Presentation**: 基於當前已收錄的 4 篇頂刊/頂會論文，編譯並產出 3D 臉部動畫前沿學術簡報投影片。
- [2026-11-23] **Ingest**: [[2026_ESGaussianFace]] - 發表於 IEEE TVCG 2026 的代表作。首個基於 3D Gaussian Splatting (3DGS) 同時實現「語音對嘴 + 任意連續情感控制 + 藝術風格遷移」的多視角可視化談話頭像架構。
- [2026-11-23] **Ingest**: [[2026_EmoPoseFace]] - 發表於 IEEE TVCG 2026 的里程碑研究。提出首個在擴散模型架構下同步協同生成「語音-情感-頭部姿態 (Head Pose)」的雙分支解耦架構。
- [2026-11-23] **Ingest**: [[2025_RefFace]] - 提出首個將臉部特徵點（Landmarks）作為獨立條件引入擴散模型的 3D 動畫生成架構。
- [2026-11-23] **Ingest**: [[2025_DiffusionTalker]] - 提出結合個人化特徵（身份+情緒對比學習）與漸進式步數/音訊雙重蒸餾的超輕量 3D 臉部動畫擴散模型。
- [2026-11-23] **Init**: 初始化 3D 臉部動畫 LLM Wiki 知識庫基礎架構、規範與目錄結構。
