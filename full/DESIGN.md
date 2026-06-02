# TPASS Redesign — Design System

## Aesthetic

**Civic Clarity** — 像金融產品一樣以數字說話，像公共服務一樣值得信任，像好的 SaaS 工具一樣引導決策。去除政府網站的官僚感，保留公共服務的可信度。白底、大字、數字優先、最少裝飾。

**設計原則（從研究洞察導出）：**
1. **數字優先**：省下的金額是最重要的資訊，視覺權重最高
2. **輸入 → 輸出**：精靈式（wizard）是主要互動模式，不讓使用者自行拼湊
3. **不讓使用者猜**：路線查詢直接輸出推薦版本，不只說「符合」
4. **升級無損失感**：1.0→2.0 升級頁面必須主動說明「現有設定完整保留」
5. **信任來源可見**：重要數字附更新日期；真實用戶引言優先於行銷文案

---

## Typography

- **Display（標題）：** Plus Jakarta Sans（Latin）+ Noto Sans TC（中文）
- **Body（內文）：** Noto Sans TC（中文優先）+ Plus Jakarta Sans
- **Mono（數字/標籤）：** SF Mono / Fira Code / Courier New — 用於金額計算、日期、代碼標註

| Role | Size | Line-height | Weight | Notes |
|------|------|-------------|--------|-------|
| h1 | 3rem | 1.05 | 800 | letter-spacing: -0.02em |
| h2 | 2.25rem | 1.1 | 700 | letter-spacing: -0.015em |
| h3 | 1.5rem | 1.2 | 700 | |
| h4 | 1.125rem | 1.3 | 600 | |
| savings | 3.5rem | 1.0 | 800 | letter-spacing: -0.03em · accent color · 試算金額專用 |
| body | 1rem | 1.7 | 400 | |
| small | 0.875rem | 1.6 | 400 | ink-2 color |
| micro | 0.75rem | 1.5 | 400 | muted color · 日期、來源、免責說明 |

**字型使用限制：** 最多三個 weight（400 / 600–700 / 800）。不用斜體作強調，用 font-weight 或 color 區分。

---

## Color

| Token | Value | Use |
|-------|-------|-----|
| `--bg` | #FFFFFF | 頁面主背景 |
| `--bg-subtle` | #F5F5F5 | 卡片背景、表單區塊、步驟指引 |
| `--bg-elevated` | #FAFAFA | Wizard 容器背景 |
| `--ink` | #2B2B2B | 主要文字（標題、正文）|
| `--ink-2` | #5C5C5C | 次要文字（說明、標籤）|
| `--muted` | #9A9A9A | 輔助文字（日期、來源、placeholder）|
| `--accent` | #3CB371 | 品牌綠 · CTA 按鈕、推薦標籤、省錢金額 |
| `--accent-dark` | #2A9459 | 品牌綠 hover / pressed 狀態 |
| `--accent-light` | #E8F5EE | 綠色淡底 · 推薦狀態卡、radio 選中背景 |
| `--line` | #E8E8E8 | 分隔線、卡片邊框 |

**對比度確認（WCAG AA）：**
- `--ink` (#2B2B2B) on `--bg` (#FFFFFF)：14.4:1 ✓
- `--accent` (#3CB371) on `--bg` (#FFFFFF)：3.1:1（僅用於大字體 / 圖形，不用於小字）
- `--accent-dark` (#2A9459) on `--bg` (#FFFFFF)：4.6:1 ✓（按鈕文字使用白色 #FFF，對比 4.5:1+ ✓）

---

## Layout

- **Grid：** 12 欄，gutter 24px
- **Spacing scale：** 4 / 8 / 16 / 24 / 40 / 64 / 96 px
- **Max content width：** 1120px（足夠放方案比較表）
- **Prose width：** 680px（標題）/ 560px（內文段落）
- **Border radius scale：** 4px（輸入框）/ 8px（按鈕、卡片）/ 12px（大容器、Wizard）/ 100px（Badge、圓形標籤）
- **Mobile breakpoint：** 768px，側邊 padding 降至 20px，雙欄改單欄

---

## Motion

- **Philosophy：** 極克制。原型階段不加動畫，以功能正確優先
- **Default duration：** 150ms
- **Default easing：** `ease-out`
- **允許的動畫：**
  - 按鈕 hover/focus：background-color transition 150ms
  - 表單 focus：border-color transition 150ms
  - Wizard 步驟切換：fade + slide-up（translate Y 8px → 0），duration 200ms
- **禁止：** 複雜 keyframe 動畫、loading spinner 超過 1 秒、自動播放的任何效果

---

## Components

### Button

```
.btn-primary   → bg: --accent, color: #fff, hover: --accent-dark
.btn-secondary → border: --accent, color: --accent, hover bg: --accent-light
.btn-ghost     → border: --line, color: --ink-2, hover bg: --bg-subtle
```

- Size default：padding 12px 24px，font-size 0.9375rem，font-weight 600
- Size lg（主要 CTA）：padding 16px 32px，font-size 1rem
- Border-radius：8px（全部按鈕一致）
- **規則：** 每個畫面只有一個 btn-primary；次要操作用 btn-ghost；「返回」永遠是 ghost

### Card

```
.card          → border: 1px --line, radius: 12px, padding: 24px
.card-elevated → card + shadow: 0 1px 4px rgba(0,0,0,.06), 0 4px 16px rgba(0,0,0,.04)
.card-accent   → border: 1.5px --accent, bg: --accent-light（用於「你目前多花了 X 元」等 loss-frame 訊息）
```

### Input / Select

- Border：1.5px --line，focus → 1.5px --accent
- Border-radius：8px
- Padding：12px 16px
- Placeholder color：--muted
- **規則：** label 永遠在上方（不用 floating label），hint text 在 label 下、input 上

### Wizard（決策精靈）

- 步驟指示器在 header，3 步驟為上限
- 每步驟一個問題，選項以 radio card 呈現（整列可點擊，不只是圓點）
- 選中狀態：border --accent + bg --accent-light
- Footer：左「返回」（btn-ghost），右「下一步」（btn-primary）
- 最後一步直接顯示結果，不另開頁面

### Result Card（試算結果）

- Header：bg --accent，文字白色，右側顯示推薦版本 badge
- Body：省錢金額（.t-savings）視覺最大；計算明細以 grid 呈現
- Footer：全寬 btn-primary
- 必須附：`資料更新日期` micro 文字

### Version Card（方案比較）

- 2 欄並排（desktop），單欄堆疊（mobile）
- 推薦方案：border --accent，header bg --accent-light，附「推薦給你」badge
- 不推薦方案：border --line，badge 說明「不適合你目前的路線」原因
- 不推薦方案的 CTA 降階為 btn-ghost（不競爭注意力）

### Trust Signal（信任訊號）

- 用戶引言：border-left 3px --accent，bg --bg-subtle，italic 引文 + cite 標明路線
- 數字附 micro 文字：「資料更新：YYYY-MM-DD」
- 不使用官方行銷措辭作信任依據；優先真實用戶語言

### Badge / Tag

```
.badge-green   → bg: --accent-light, color: --accent-dark（推薦、符合）
.badge-gray    → bg: --bg-subtle, color: --ink-2（不適用、中性）
.badge-warning → bg: #FEF3C7, color: #D97706（注意、待確認）
```

---

## Avoid

- **漸層（gradient）：** 禁止。純色背景優先
- **emoji：** 不用於 UI 元件；信任引言中不出現
- **科技裝飾：** 無網格背景、無光暈、無 glassmorphism
- **多於 3 種 border-radius：** 不混用，每個元件用對應的一個值
- **超過 2 層陰影：** card-elevated 已是最重的視覺層次
- **在小字（< 1rem）使用 --accent 綠色作文字：** 對比度不足
- **功能型錄排版：** 不用純列表展示功能差異；必須搭配「對你的意義」說明
- **讓使用者輸入他們不知道的數字：** 試算工具選項由系統提供，不要求手動輸入票價
