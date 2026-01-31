# tw-stock-dashboard（投資監控中心）

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



