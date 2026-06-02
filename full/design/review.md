# Design Review: TPASS prototype.html

**Date:** 2026-06-01
**Reviewer:** Claude (designer's-eye pass)
**Viewports captured:** 1440×900 · 768×1024 · 375×812 + Step 3 state + full-page scroll
**Sister doc:** `heuristic-eval.md`（可用性問題）— 本文件聚焦視覺與排版，不重複 heuristic 發現

---

## Summary

| 嚴重度 | 數量 |
|--------|------|
| [3] Major | 1 |
| [2] Moderate | 4 |
| [1] Minor | 5 |

**Top 3 優先修復：**
1. **[3][desktop] 結果卡三色疊帶** — heuristic fix 產生的視覺副作用，視覺上讀起來像施工中
2. **[2][desktop] 英雄區左欄垂直定位** — 內容偏低，上方留白過多，頁面像「空著」
3. **[2][mobile] Wizard 被推到螢幕外** — 375px 上 Hero 佔半屏，主要操作區需要捲動才看到

---

## 視覺一致性（Visual Consistency）

### [3][desktop/all] 結果卡出現三個相鄰色帶
`review-step3.png`

**問題：** 結果卡 header 為 `#3CB371`（accent），緊接著一條 `#2A9459`（accent-dark）解釋說明帶，再接白色 body。三個不同背景色堆疊在同一個卡片頂部，看起來像色版疊錯，或網站局部沒載入完成。這是 heuristic-eval 的 H2 修復（加 1.0 說明）引入的視覺副作用。

**Fix：** 把解釋說明移入卡片 body，用 `--accent-light` 底色的 inline callout 替代：
```html
<!-- 放在 savings-row 之前 -->
<div style="background:var(--accent-light);border-radius:6px;padding:10px 14px;margin-bottom:16px;font-size:.8125rem;color:var(--accent-dark);line-height:1.6;">
  1.0 = 台北都會區月票 $799 &nbsp;·&nbsp; 2.0 = 跨縣市月票 $1,200（你的路線用 1.0 就夠）
</div>
```
同時移除 accent-dark 背景帶，讓 header 只有一個綠色面積。

---

### [1][all] 眉標（eyebrow）與 nav logo 重複品牌名
`review-desktop.png`

**問題：** Nav logo 已顯示「TPASS」，hero 眉標又寫「TPASS 通勤月票」，兩者垂直距離約 100px，使用者在同一視野內看到品牌名兩次。眉標的「TPASS」是多餘的，「通勤月票」才是 contextual value。

**Fix：** 眉標改為「通勤月票方案試算」或僅「通勤月票」，移除 TPASS 重複。

---

### [1][all] Wizard footer 雙重進度指示器
`review-desktop.png`

**問題：** Wizard 同時有頂部 step tabs（1路線 / 2頻率 / 3結果）和底部文字「第 2 步，共 3 步」兩套進度呈現。資訊重複，視覺上讓 footer 顯得忙碌。

**Fix：** 移除 footer 中的「第 N 步，共 3 步」文字。Step tabs 本身已足夠清楚，不需要冗餘標記。

---

## 層次與排版（Hierarchy & Layout）

### [2][desktop] 英雄區左欄：內容偏低，頂部留白過多
`review-desktop.png`

**問題：** Hero-left 使用 `justify-content: center`，在 836px（100vh - 64px）的容器中，內容塊（eyebrow + h1 + desc + stats ≈ 350px）被垂直置中，導致頂部約有 243px 的白色空間。頁面視覺重心偏低，第一眼印象是「空著」，不像一個明確邀請用戶採取行動的 landing page。

**Evidence:** 截圖中，h1「你每個月可以省多少？」的頂部距離 nav 底部約 280px，超過 30% 的畫面高度是空白。

**Fix:** 將 hero-left 改為 `justify-content: flex-start; padding-top: clamp(48px, 10vh, 96px)`。讓標題在視野上三分之一區域出現，引導視線自然往右移向 wizard。

---

### [2][mobile] Wizard 主操作區被推到螢幕折疊線以下
`review-mobile.png`

**問題：** 375px 版面改為單欄堆疊，Hero 佔據視口前半段（eyebrow + h1 + desc + trust stats + padding ≈ 440px），Wizard 在螢幕折疊線以下，首屏看不到。這意味著手機用戶需要先捲動才能開始操作——而 wizard 才是這個頁面的核心互動。

**Fix（擇一）：**
- 方案 A：在 mobile 隱藏 hero-left 的 trust stats（`display:none @480px`），減少 hero 高度，讓 wizard 提早出現
- 方案 B：將 hero section 的 `min-height` 在 mobile 改為 `auto`（目前繼承了 `calc(100vh - 64px)`，把整個 hero 撐到全屏）

---

### [1][desktop] 左右欄比例失衡：左欄 960px 對應右欄 480px
`review-desktop.png`

**問題：** `grid-template-columns: 1fr 480px` 在 1440px 螢幕上讓左欄達 960px。這個寬度只放了約 460px 最大寬的文字內容（`max-width: 460px` 的 hero-desc），右側約 500px 是純白空白。左欄的空曠讓人注意到它的空，而非它的內容。

**Fix:** 加上 `max-width: 1200px; margin: 0 auto` 讓 hero 區塊居中，或改為 `grid-template-columns: 1fr 520px`，視覺比例更接近黃金比例（1:0.618）。

---

## 間距（Spacing）

### [2][desktop/step3] 結果卡 body：費用明細與申辦按鈕間距壓縮
`review-step3.png`

**問題：** Step 3 的結果卡在加入解釋說明帶後，body 可用高度縮短。費用明細 grid（三行數字）與「立即申辦」按鈕之間的間距看起來緊（約 12px），而按鈕下方的備註文字又很小，整個 body 的節奏顯得局促——上面說明帶大片深色，下面白色區塊擠在一起。

**Fix:** 解釋說明改用 inline callout（見 [3] 修復方案）後，card body 恢復空間，這個問題自動解除。

---

### [1][desktop] Wizard 面板：Step 2 radio cards 以下有過多空灰區
`review-desktop.png`

**問題：** 4 個 radio card 結束後，wizard body 的下方還有約 150px 的 `--bg-subtle` 灰色空白，才到白色 footer。這個空灰區沒有內容，讓面板看起來「半滿」，重心飄移到底部 footer。

**Fix:** 為 `.wizard-panel` 設定固定高度或 `max-height`，或將 `.w-body` 改為 `overflow-y: auto`，讓內容量自然決定 body 高度，footer 貼緊內容。目前 `flex: 1` 讓 body 撐滿剩餘高度是問題根源。

---

## 響應式（Responsive）

### [2][tablet/768px] 切換至單欄後 hero 上方有多餘 padding
`review-tablet.png`

**問題：** 768px breakpoint 時 hero 改為單欄，但 `.hero-left` 的 `padding: 56px 32px` 搭配 `justify-content: center` 仍然讓標題上方留下大量空白（約 120px）。Tablet 用戶的首屏體驗與桌面版有同樣的「從空白開始」問題。

**Fix:** 同 [2][desktop] 英雄區修復，改用 `padding-top: clamp(40px, 8vh, 64px)` + `justify-content: flex-start`，統一覆蓋所有斷點。

---

## AI Slop 檢查

### [1][all] `⊕` 作為「站點適用」圖示
Trust strip 第三項使用 `⊕`（加圓符號 U+2295）代表「3,500+ 站點適用」。這個符號原本是數學/邏輯上的「exclusive or」，用作交通覆蓋圖示語意不清，且在不同作業系統字型下渲染效果不一。

**Fix:** 以簡單的 SVG inline icon 替代，或改用文字圖示「◎」（更接近「站點」概念）、或直接去掉圖示只保留文字。目前其他兩個信任項目的圖示（✓ 和 ↺）也是 Unicode 字符，一致性上沒問題，但 ⊕ 的語意最弱。

**其他 AI slop 檢查結果（無問題）：**
- 無不必要的漸層 ✓
- 無 lorem ipsum ✓
- 無裝飾 emoji ✓
- 無 Bootstrap 預設感 ✓
- Border-radius 使用一致（8px / 12px / 16px，各有對應元件層級）✓
- 對齊方向一致（全左對齊）✓

---

## 與 heuristic-eval.md 的對照

本次發現未與 heuristic-eval 重複。兩份文件合併閱讀：

| 本 review（視覺） | heuristic-eval（可用性）|
|-----------------|----------------------|
| 結果卡三色帶（副作用）| H2 已修復但引入視覺問題 |
| 左欄頂部留白 | H7 Wizard 缺少「略過」選項 |
| Mobile wizard 位置 | H1 系統狀態 / H3 控制自由度 |
| Wizard 灰色空區 | — |

---

## 修復優先序

| 優先 | 嚴重度 | Viewport | 問題 | 修復成本 |
|------|--------|----------|------|---------|
| 🔴 1 | [3] | all | 結果卡三色帶 | 低（改 HTML 結構）|
| 🔴 2 | [2] | desktop | Hero 左欄頂部留白 | 極低（改一行 CSS）|
| 🔴 3 | [2] | mobile | Wizard 被推到折疊線以下 | 低（CSS min-height fix）|
| 🟡 4 | [2] | tablet | Tablet hero 上方留白 | 極低（同 #2，統一處理）|
| 🟡 5 | [2] | step3 | 結果卡 body 間距局促 | 自動解除（隨 #1 修復）|
| 🟢 6 | [1] | desktop | 左右欄 960/480 比例 | 低（加 max-width）|
| 🟢 7 | [1] | all | Wizard 灰色空區 | 低（修 flex 邏輯）|
| 🟢 8 | [1] | all | 眉標重複品牌名 | 極低（改文字）|
| 🟢 9 | [1] | all | 雙重進度指示器 | 極低（移除文字）|
| 🟢 10 | [1] | all | ⊕ 圖示語意不清 | 低（換字符或 SVG）|
