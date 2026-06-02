# Heuristic Evaluation: TPASS Prototype

**Date:** 2026-06-01
**Evaluator:** Claude (vanilla heuristic eval · Nielsen 10)
**Scope:** `design/prototype.html` — 3-step wizard homepage，評估 4 個狀態：Step 1（路線輸入）、Step 2（頻率選擇）、Step 3（試算結果）、Mobile 375px

---

## Summary

| 嚴重度 | 數量 | Heuristics |
|--------|------|-----------|
| Critical (4) | 0 | — |
| Major (3) | 0 | — |
| Moderate (2) | 4 | H1, H2, H3, H5 |
| Minor (1) | 5 | H4, H6, H7, H8, H10 |
| N/A (0) | 1 | H9 |

**Top 3 優先修復：**
1. **H5** — Step 1 空白欄位可直接跳下一步，無欄位驗證（影響所有潛在用戶）
2. **H2** — 結果卡「推薦 TPASS 1.0」未解釋 1.0 是什麼，與用戶研究的核心卡點直接衝突
3. **H3** — Step 分頁 tab 有視覺互動感（hover 樣式）但實際不可點擊，製造假 affordance

---

## H1：Visibility of System Status（系統狀態能見度）

**Score: 2 — Moderate**

**Finding 1：Mobile 上步驟 tab 第 3 步標籤被截斷**
Step tab 在 375px 視窗下，「3 試算結果」的文字因空間不足被截斷，使用者只能看到「3」數字，無法確認第三步是什麼。進度感知受損。

**Evidence:** `heur-mobile.png` — 右側 wizard panel step tab 區，第三個 tab 僅顯示「3」。

**Fix:** 在 `≤ 480px` 時，將 step tab 標籤改為只顯示數字圓點（step-dot 形式），或縮短標籤文字為「路線 / 頻率 / 結果」（各兩字）。

---

**Finding 2：Step 1「← 上一步」禁用狀態視覺權重不足**
Step 1 的「← 上一步」按鈕雖已設 `disabled` attribute，但視覺上僅以低透明度區分，沒有明確的「無法使用」語意提示。使用者可能誤以為自己沒有看到按鈕，或按鈕有 bug。

**Evidence:** `heur-step1.png` — wizard footer，「← 上一步」視覺呈現灰淡但仍是按鈕形狀。

**Fix:** 對 `disabled` 按鈕加 `cursor: not-allowed`，或完全隱藏 Step 1 的返回按鈕（`visibility: hidden`），減少使用者困惑。

---

## H2：Match Between System and Real World（符合現實世界慣例）

**Score: 2 — Moderate**

**Finding：結果卡「推薦 TPASS 1.0」缺少情境說明**
Step 3 結果卡 header 顯示「推薦 TPASS 1.0」，但對潛在用戶（Persona 1）而言，「TPASS 1.0」這個詞本身就是他們最困惑的點之一。訪談研究顯示受訪者普遍說不清楚 1.0 和 2.0 的差別；到達結果頁時，他們需要立刻理解這個推薦的含義，而目前的呈現沒有提供任何錨點。

**Evidence:** `heur-step3.png` — 結果卡 header badge「推薦 TPASS 1.0」，卡片內文無任何解釋。

**Fix:** 在 badge 下方或「立即申辦」前加一行說明文字：「TPASS 1.0 = 台北都會區不限次數月票，月費 $799，適合你目前的通勤路線。」一句話即可，不需要完整說明頁面。

**附：做得好的地方**
結果卡中「竹北站 → 新竹站，每週 5 天」的路線回顯，以及「等於每年省 $4,104」的年化換算，都是非常自然的現實世界語言。Trust stat「97% 辦卡後持續使用率」的企業腔可考慮改為「97% 辦了繼續在用」。

---

## H3：User Control and Freedom（使用者控制與自由）

**Score: 2 — Moderate**

**Finding：Step 分頁 tab 有 hover 感但不可點擊**
三個 step tab（1通勤路線 / 2搭乘頻率 / 3試算結果）在視覺上呈現為可互動的分頁格式，且 CSS 設有 `cursor: pointer`（繼承自父元素）。使用者可能嘗試點擊「3試算結果」來跳過前兩步，但點擊後無反應，產生困惑。這是一個 false affordance。

**Evidence:** `heur-desktop.png`（Step 2 狀態）— step tab 在視覺上看起來像可點擊的分頁，特別是已完成的「1通勤路線」有 done 樣式，暗示可以回去點它。

**Fix（擇一）：**
- 方案 A（推薦）：讓已完成的 tab（done state）可點擊，允許用戶直接跳回前面的步驟
- 方案 B：移除 tab 的互動外觀，改為純視覺進度指示器（類似 step dot 形式，無 hover 效果，cursor 設為 default）

---

**Finding 2：Wizard 沒有「略過」或「離開」選項**
進入 wizard 後，使用者無法直接跳出查看完整方案介紹，只能一步一步走或使用 nav 連結離開。對於已有初步了解、只想確認某一個細節的用戶，缺乏逃生口。

**Evidence:** 全部截圖 — wizard panel 內無「直接看方案比較」或「略過試算」連結。

**Fix:** 在 wizard header 的「找出適合你的方案」標題旁加一個低調連結：「直接比較 1.0 / 2.0 →」，連到頁面下方的方案說明區塊。

---

## H4：Consistency and Standards（一致性與標準）

**Score: 1 — Minor**

**Finding：Step tab 數字與標籤間距不一致**
「1 通勤路線」（數字後有空格）vs「2搭乘頻率」「3試算結果」（數字緊接文字），在 HTML 原始碼中呈現不一致，雖然視覺差異微小，但屬於排版細節疏漏。

**Evidence:** `heur-desktop.png` — wizard step tab 區。

**Fix:** 統一在 HTML 或 CSS 中處理 tab 標籤格式，建議格式為「1. 通勤路線」或「① 通勤路線」，全部一致。

---

## H5：Error Prevention（防止錯誤）

**Score: 2 — Moderate**

**Finding：Step 1 空白表單可直接前進**
Step 1 的「出發站」、「到達站」為空白 text input，「主要搭乘交通工具」select 預設為「請選擇」（空值）。目前「下一步 →」按鈕在所有欄位皆空的情況下仍可點擊，用戶可以進入 Step 2 而不填任何路線資訊。如此到了 Step 3，結果仍顯示 hardcoded 的「竹北站 → 新竹站」，與實際未輸入的情況不符，製造誤解。

**Evidence:** `heur-step1.png` — Step 1 表單所有欄位為空，footer 「下一步 →」為綠色可點擊狀態。

**Fix（最低成本版本）：** 監聽 Step 1 的 `input` / `change` 事件，若「出發站」或「到達站」為空，則將「下一步 →」設為 `disabled`，並加上 hint「請填入通勤路線再繼續」。

**Fix（進階版本）：** 加入站名 autocomplete，從預設的站名清單中選取，避免用戶輸入無效站名進入後續計算流程。

---

**附：做得好的地方**
Step 2 的 radio group 預設選取「5天（週一至週五）」，確保使用者不會在 Step 2 產生空值，是良好的錯誤預防設計。

---

## H6：Recognition Rather Than Recall（辨識而非記憶）

**Score: 1 — Minor**

**Finding：結果頁未說明推薦依據**
Step 3 結果卡推薦「TPASS 1.0」，但用戶不知道系統是基於什麼標準做出這個推薦（是路線？是縣市？是費用比較？）。若用戶在結果出現時想理解「為什麼不是 2.0？」，沒有任何視覺線索引導他找到答案。

**Evidence:** `heur-step3.png` — 結果卡未顯示推薦邏輯。

**Fix:** 在費用明細下方加一行：「2.0 月費 $1,200 · 你的路線在 1.0 範圍內，升級不增加節省」，讓比較結果可見，不讓用戶自行記憶或猜測。

---

**附：做得好的地方**
- Radio cards 每張都有主標籤 + 副標題，不需記憶選項含義
- Step 3 將用戶輸入回顯（「竹北站 → 新竹站，每週 5 天」），符合 recognition 設計原則

---

## H7：Flexibility and Efficiency of Use（彈性與效率）

**Score: 1 — Minor**

**Finding：已有 1.0 的用戶快速入口效果有限**
Nav 右上角「已有 1.0，查升級」按鈕為 Persona 2（現狀守衛）提供直接入口，設計意圖良好。但點擊後連結到哪裡在 prototype 中無法驗證。若該按鈕僅捲動到頁面某區塊而非進入專屬的升級試算流程，對這類用戶的效率提升有限。

**Evidence:** `heur-desktop.png` — nav 右側按鈕，目前在 prototype 為靜態。

**Fix:** 確保「已有 1.0，查升級」連結到一個完整的「你目前的 1.0 費用 vs 升 2.0 的差距」計算流程，而非通用首頁。這是 Persona 2 的核心需求（synthesis Theme 4）。

---

## H8：Aesthetic and Minimalist Design（美觀與極簡設計）

**Score: 1 — Minor**

**Finding：兩個「平均省錢數字」並列易造成混淆**
Hero 左欄信任數據顯示「$342 / 竹北→新竹台鐵通勤平均每月省」，而頁面下方 trust strip 顯示「平均月省 $280 / 依實際搭乘路線計算」。兩個數字同為「平均省錢」，但金額不同，且 $342 帶有路線說明而 $280 沒有，容易讓用戶困惑「哪個才是真的？」

**Evidence:** `heur-desktop.png`（hero 區）與頁面下方 trust strip（同一個截圖可見 hero 內的 $342）。

**Fix（擇一）：**
- 移除 trust strip 中的 $280，改為其他非重複的信任數據（如「申辦僅需 5 分鐘」、「全台 6,500+ 便利商店可辦」）
- 或統一兩個數字，並附上相同的說明語境

---

**附：做得好的地方**
- 每個步驟聚焦於一個問題，wizard body 無多餘資訊
- 結果卡層次清晰：大金額 → 路線說明 → 費用明細 → CTA，信息密度合理

---

## H9：Help Users Recognize, Diagnose, and Recover from Errors（錯誤識別與恢復）

**Score: 0 — N/A**

此為靜態 HTML prototype，無法觸發真正的錯誤狀態（無表單驗證、無網路請求、無 API 錯誤）。本條在當前範疇不適用。

**實作階段需留意：**
- Step 1 站名不在資料庫時的錯誤訊息（「找不到此站名，請確認是否為大眾運輸站點」）
- 試算失敗時的降級處理（「無法即時計算，以下為該路線平均估算值」）

---

## H10：Help and Documentation（說明與文件）

**Score: 1 — Minor**

**Finding：推薦結果無「為什麼是這個版本」的解釋入口**
Step 3 的推薦結果是整個 wizard 流程的終點，也是最多用戶會想「我能不能多了解一點」的地方。目前結果卡沒有「了解 1.0 的完整說明」連結，也沒有說明「為什麼不是 2.0」。用戶若有疑問只能自行去導覽列找「方案介紹」。

**Evidence:** `heur-step3.png` — 結果卡底部僅有升級 2.0 的連結，無「詳細了解 1.0」。

**Fix:** 在結果卡「試算說明」備註旁加一個低調連結：「了解 TPASS 1.0 完整說明 →」，連到方案介紹頁或頁面內的錨點說明。

---

**附：做得好的地方**
- Wizard 每步驟的 hint text 都是良好的 inline documentation（「輸入你每天的起點和終點就好，轉乘站不用填」）
- 「資料更新：2026-05-01」的透明度標記符合公共服務設計的信任需求
- 備註文字「試算以系統平均票價估算，實際金額依搭乘次數略有差異」準確設定期望

---

## 修復優先序

| 優先 | Heuristic | 問題 | 修復成本 |
|------|-----------|------|---------|
| 🔴 1 | H5 | Step 1 空欄可前進 | 低（JS disabled 狀態控制）|
| 🔴 2 | H2 | 結果卡未解釋「1.0 是什麼」 | 低（加一行文字）|
| 🔴 3 | H3 | Step tab 假 affordance | 低（改 cursor 或補點擊邏輯）|
| 🟡 4 | H1 | Mobile tab 文字截斷 | 低（CSS 調整）|
| 🟡 5 | H8 | 兩個不同「平均省錢」數字 | 低（移除一個或統一）|
| 🟡 6 | H3 | Wizard 缺乏「略過」選項 | 中（加連結 + 錨點）|
| 🟢 7 | H6 | 結果未顯示推薦依據 | 低（加比較文字）|
| 🟢 8 | H10 | 結果卡無「了解更多」連結 | 低（加連結）|
| 🟢 9 | H4 | Tab 數字格式不一致 | 極低（HTML fix）|
| 🟢 10 | H7 | 1.0 升級入口實作待確認 | 中（需設計升級流程）|

---

## 與 synthesis.md 的對照

| Heuristic 發現 | 對應研究洞察 |
|---------------|------------|
| H2：結果卡未解釋 1.0 | Theme 3：路線與版本適用性長期模糊；P1「我不知道 1.0 是什麼」 |
| H5：空欄可前進 | Theme 2：站在用戶角度，系統應主動防止輸入錯誤；Opportunity 1 提到輸入→輸出的嚴謹性 |
| H3：tab 假 affordance | 使用者旅程「理解差異」階段：介面暗示可操作但實際不行，加深困惑感 |
| H8：兩個省錢數字 | Theme 1：ROI 計算牆——數字互相矛盾只會讓用戶更不信任 |
