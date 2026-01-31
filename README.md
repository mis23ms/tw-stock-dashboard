# tw-stock-dashboard（投資監控中心）
tw-stock-dashboard/README.md（總覽頁）
# tw-stock-dashboard（投資監控中心 / 總覽頁）

本 repo 是「總覽頁 Dashboard」，把三個資料 repo 產出的 `summary.json` 整合顯示在同一頁：
- 今日重點（highlights）
- 各來源更新時間（updatedAt）

✅ Dashboard 不跑資料、不需要 GitHub Actions。  
✅ 你只要打開 / 重新整理 Dashboard，就會重新抓三個來源 summary。

---

## 入口（你要看結果就看這裡）
- Pages: https://mis23ms.github.io/tw-stock-dashboard/

---

## 資料來源（Dashboard 會 fetch 這三個 summary.json）
- 股票外資（tw-stock-06）
  - https://mis23ms.github.io/tw-stock-06/summary.json
- 期貨大額交易人（tw-stock-futures）
  - https://mis23ms.github.io/tw-stock-futures/summary.json
- 選擇權大額交易人（tw-stock-options）
  - https://mis23ms.github.io/tw-stock-options/summary.json

---

## summary.json 格式（Dashboard 只認這個）
```json
{
  "updatedAt": "2026-01-31T22:08:09+08:00",
  "highlights": [
    "🚨 台積電：收 1775 / 漲跌 -1.66% / 外資D0 -13718",
    "台積電期貨：Top5淨 -1344 / Top10淨 -6131",
    "台積電選擇權：Call淨 120 / Put淨 -155 / 差 275 → 偏多"
  ]
}

哪個檔案是入口？（只要看這個）

index.html

<script> 裡的 SOURCES 決定抓哪些 summary

重新整理頁面 = 重新抓資料（前端有 cache-bust）

常見問題（Troubleshooting）
A) Dashboard 沒更新

先直接打開三個 summary.json，看 updatedAt 是否已變新

強制重新整理：

Windows：Ctrl + F5

Mac：Cmd + Shift + R

B) 今日重點出現「(找不到 highlights)」

代表來源 summary.json 格式不對（缺 highlights 或不是陣列）
→ 回到來源 repo 修 build_summary 產出。

C) summary.json 直接 404

代表來源 repo 沒把 summary.json 放到 Pages 可讀的位置
→ 先確認檔案有 commit，且 Pages 設定路徑正確。

修復優先順序（給 AI/未來自己）

先看來源：三個 summary.json 是否正確

再看 Dashboard：SOURCES URL 是否正確、Console 是否有 fetch error


---

## 2) `tw-stock-06/README.md`（股票外資 / 現貨）

```md
# tw-stock-06（股票外資戰報 / 現貨資料）

本 repo 會用 GitHub Actions 定時更新資料，輸出到 GitHub Pages。
Dashboard（tw-stock-dashboard）會讀取本 repo 的 `summary.json` 來顯示「今日重點」。

---

## 入口
- Repo: https://github.com/mis23ms/tw-stock-06
- Pages: https://mis23ms.github.io/tw-stock-06/
- Summary（給 Dashboard 用）: https://mis23ms.github.io/tw-stock-06/summary.json

---

## 你只要知道的資料輸出（重要）
- `summary.json`
  - 小檔、給 dashboard 用（updatedAt + highlights）
- `docs/*.json`
  - 大檔/結構化資料（網站或未來總表可能會用）

---

## 自動更新流程（你要查問題就照這個順序）
1. `.github/workflows/update.yml` 觸發（排程或 workflow_dispatch）
2. 跑 Python 抓資料/整理資料
3. 產出或更新 `docs/*.json`
4. 跑 build summary（產出 `summary.json`）
5. commit & push
6. GitHub Pages 更新（Pages 上的 json 變新）

---

## 你最常會改的地方
### A) 改監控股票清單
在 repo 內搜尋（GitHub 搜尋框）：
- `WATCHLIST` / `TARGETS` / `tickers` / 直接搜 `2330`

原則：股票清單只應該改一個地方。

### B) 改摘要（今日重點）
- 產生 `summary.json` 的腳本（通常叫 `build_summary.py` 或類似）
- highlights 的文字內容與條數都在這裡決定

---

## 常見錯誤分類（看 log 就知道是哪種）
### 1) 資料問題
HTTP 403/404/500、抓到空資料、欄位變動、反爬  
→ 先看 Actions log 哪一步抓不到

### 2) 程式問題
SyntaxError / NameError / KeyError  
→ log 會指到哪個 `.py` 第幾行

### 3) 部署/路徑問題
Actions 綠燈但 Pages 上的 json 沒更新、或 404  
→ 檢查是否有 commit 到正確檔案與 Pages 設定

---

## Debug 最快路徑（不用猜）
1) 先打開 `summary.json` 看是否新資料  
2) 不新 → 去 Actions 看最新 run 的紅點  
3) 新但 dashboard 不顯示 → dashboard 的 SOURCES / Console / 強制刷新


這個專案是一個「總覽頁 Dashboard」，把三個監控專案的重點訊息（`summary.json`）整合在同一頁顯示，讓我一眼看出 4 檔重點股的現貨/期貨/選擇權狀態。

✅ Dashboard 只做兩件事：
1. 抓取三個來源的 `summary.json`
2. 把 `highlights` 逐條列出，並顯示各來源 `updatedAt`

---

## 1) 相關 Repo / Pages

### Dashboard（本 repo）
- Repo: https://github.com/mis23ms/tw-stock-dashboard
- Pages: https://mis23ms.github.io/tw-stock-dashboard/

### 來源資料（3 個監控專案）
- 股票外資（stock-06）
  - Pages: https://mis23ms.github.io/tw-stock-06/
  - Summary: https://mis23ms.github.io/tw-stock-06/summary.json

- 期貨大額交易人（futures）
  - Pages: https://mis23ms.github.io/tw-stock-futures/
  - Summary: https://mis23ms.github.io/tw-stock-futures/summary.json

- 選擇權大額交易人（options）
  - Pages: https://mis23ms.github.io/tw-stock-options/
  - Summary: https://mis23ms.github.io/tw-stock-options/summary.json

---

## Dashboard 使用的資料格式（非常重要）

Dashboard **只依賴**每個 repo 的 `summary.json`，格式如下：

```json
{
  "updatedAt": "2026-01-31T22:08:09+08:00",
  "highlights": [
    "🚨 台積電：收 1775 / 漲跌 -1.66% / 外資D0 -13718",
    "台積電期貨：Top5淨 -1344 / Top10淨 -6131",
    "台積電選擇權：Call淨 120 / Put淨 -155 / 差 275 → 偏多"
  ]
}


Dashboard 的資料來源設定在哪？

在 index.html 裡 <script> 的 SOURCES：

const SOURCES = [
  { name: "股票外資", url: "https://mis23ms.github.io/tw-stock-06/summary.json", page: "https://mis23ms.github.io/tw-stock-06/" },
  { name: "期貨",     url: "https://mis23ms.github.io/tw-stock-futures/summary.json", page: "https://mis23ms.github.io/tw-stock-futures/" },
  { name: "選擇權",   url: "https://mis23ms.github.io/tw-stock-options/summary.json", page: "https://mis23ms.github.io/tw-stock-options/" },
];


Dashboard 會用 fetch(url + "?t=" + Date.now()) 避免快取，並把 highlights 全部列出。

4) 常見問題（Troubleshooting）
A) Dashboard 內容沒更新

先檢查三個來源的 summary.json 是否可直接打開

用強制重新整理：

Windows：Ctrl + F5

Mac：Cmd + Shift + R

B) 今日重點出現「(找不到 highlights)」

代表該來源的 summary.json 格式不符合（缺 highlights 或不是陣列）。
請回到來源 repo，修正產出的 summary 格式。

C) Actions 綠燈，但 summary.json 沒變

通常是：

workflow 沒有把 docs/summary.json commit/push

summary 檔案寫到錯誤路徑

Pages 指到的 branch/folder 不對

## AI 維修指引（給未來要修的人看）

如果 Dashboard 顯示不正常，請依序檢查：

先看來源 JSON

tw-stock-06/summary.json

tw-stock-futures/summary.json

tw-stock-options/summary.json

確認 JSON 格式

必須有 updatedAt（字串）

必須有 highlights（字串陣列）

再看 dashboard

tw-stock-dashboard/index.html 的 SOURCES URL 是否正確

extractHighlights() 是否是直接讀 j.highlights

原則：Dashboard 不應該解析 docs/data.json / docs/*_data.json 這種大檔；只吃 summary。

## 後續擴充方向（可選）

做「總表（Super Table）」：用 ticker(2330/2317/3231/2382) 合併三方資料成同一列

做「訊號燈」：期貨 + 選擇權 同向給紅/綠/黃

做「大盤氣氛」：外資台指期淨空單、P/C Ratio、散戶小台多空比等



