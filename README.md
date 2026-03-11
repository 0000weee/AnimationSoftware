🎬 PointerPro Animate
Web Animation Backend System

PointerPro Animate 是 Memory Leak Studios 開發的 網頁動畫服務後端系統。

UI/UX 團隊負責前端介面，而本專案負責：

高效能 動畫影格生成

Sprite 管理

動畫運動計算

影格合成 (Frame Composition)

記憶體最佳化

系統需與 UI/UX 團隊提供的 API 進行整合。

📖 系統概念

動畫（Animation）是一系列 影格（Frame） 依照固定時間間隔播放所形成的視覺效果。

每個影格都是一個 2D 像素矩陣（Bitmap）。

Frame
+----------------------+
| pixel pixel pixel    |
| pixel pixel pixel    |
| pixel pixel pixel    |
+----------------------+

每個像素都包含一個 顏色值。

🎨 ARGB32 色彩格式

本系統使用 ARGB32 格式表示顏色。

通道	位元
Alpha (透明度)	8 bit
Red	8 bit
Green	8 bit
Blue	8 bit

四個通道會被封裝成：

uint32_t

Alpha 通道用於表示像素透明度。

🧩 Sprite（精靈圖）

Sprite 是動畫中的 基本圖形單位，可以被重複使用。

Sprite 可以來自：

使用者提供的 ARGB32 Bitmap

程式生成圖形

目前支援的圖形：

Shape	支援
Rectangle	filled / unfilled
Circle	filled

系統設計需 方便未來新增更多圖形類型。

💾 記憶體效率設計

同一個 Sprite 可能：

在 同一動畫中出現多次

在 不同動畫中重複使用

因此系統必須確保：

Sprite bitmap 只儲存一次

建議設計方式：

Sprite Pool

Shared Pointer / Reference

Central Sprite Repository

這樣可以避免大量重複資料佔用記憶體。

🚀 Sprite 運動模型

Sprite 位置依照簡化的物理公式計算：

p = p0 + v*t + (a*t^2)/2
變數	意義
p0	初始位置
v	速度
a	加速度
t	時間

此模型可模擬：

等速運動

加速

減速

目前版本 不需要自訂動畫引擎。

🖼️ 影格生成流程

動畫影格生成流程如下：

Sprites
   ↓
Placement + Motion Calculation
   ↓
Sprite Rendering
   ↓
Frame Buffer
   ↓
Animation Frame Output

具體步驟：

建立指定大小的 Canvas

計算時間 t 時各 Sprite 的位置

依順序將 Sprite 繪製到 Frame

處理 Sprite 重疊

🔍 Alpha 處理規則

系統使用 簡化透明模型：

Alpha	行為
0x00	完全透明 → 不繪製
>0	完全不透明 → 繪製

注意：

不需要 Alpha Blending

若 pixel 被繪製，最終 alpha 必須為：

alpha = 0xFF
🧱 Sprite 重疊

Sprite 可能在畫布上互相覆蓋。

渲染順序會影響最終畫面：

Sprite A
   ↓
Sprite B
   ↓
Sprite C

後繪製的 Sprite 會覆蓋前面的內容（若 pixel 不透明）。

🔌 API 整合

後端系統需遵循 UI/UX 團隊提供的 API。

API 文件：

使用 Doxygen 格式

隨專案 source code 提供

若 API 更新，後端系統可能需要相應修改。

🎓 COMP9017 額外要求

以下函式 僅 COMP9017 學生需要實作：

void animate_placement_up(struct sprite_placement*);
void animate_placement_down(struct sprite_placement*);

COMP2017 學生：

不需要實作

即使實作也 不計分

但建議完成以保持系統完整性。

📦 系統功能總覽

PointerPro Animate 後端需支援：

ARGB32 bitmap sprite

程式生成圖形（rectangle / circle）

Sprite 記憶體共享

物理公式運動模型

Sprite layering

Alpha transparency

UI API 整合

💡 設計目標

高效能動畫生成

可擴充圖形架構

最佳化記憶體使用

清晰的 rendering pipeline

(以上為LLM協助整理，僅供個人使用)