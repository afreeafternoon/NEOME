[README.md](https://github.com/user-attachments/files/31327813/README.md)

# NEONE Movie Insights

用 [TMDb API](https://www.themoviedb.org/documentation/api) 隨機推薦電影的純前端網頁，部署在 GitHub Pages 上。

## 檔案結構

```
.
├── index.html   結構（HTML），不含任何樣式或邏輯，只透過 <link>/<script> 引入下面兩個檔案
├── style.css    所有自訂樣式（版面、字級、動畫、響應式規則）
├── app.js       所有互動邏輯（抽片流程、篩選面板、TMDb API 呼叫、動畫時序）
└── README.md    本文件
```

三個檔案職責分離，之後要調整畫面找 `style.css`、要調整行為找 `app.js`、要調整結構找 `index.html`，不用再從一整個上千行的檔案裡面找。

Tailwind CSS 走 CDN（`<script src="https://cdn.tailwindcss.com">`），即時把用到的 class 編譯成樣式，所以頁面上大部分排版還是直接寫 Tailwind class，`style.css` 只放 Tailwind 覆蓋不到、或需要 `clamp()`／媒體查詢精細控制的部分。

## 修改指南

- **改文字、排版、間距、字級**：先找 `index.html` 裡對應的元素，大部分排版是 Tailwind class（例如 `flex`、`text-sm`、`mt-4`），直接改 class 即可；如果是 `clamp()` 流體字級或行動端專用的細部調整，去 `style.css` 找對應的 class 名稱（例如 `.hero-title-fluid`、`.hero-desc-fluid`）。
- **改抽片邏輯、篩選條件、動畫時序**：都在 `app.js`。
- **新增電影類型／語系選項**：`index.html` 裡搜尋 `data-select="genre"` 或 `data-select="lang"`，照現有的 `<div class="select-option" data-value="...">` 格式增加選項；`data-value` 要填 TMDb 對應的 genre id 或 ISO 639-1 語言代碼。若新增語系，記得同步更新 `app.js` 裡的 `langDict`，否則電影卡片上會顯示語言代碼而不是中文名稱。

## 安全性說明（請詳讀）

### TMDb API 金鑰目前是公開的

`app.js` 裡的 `TMDB_API_KEY` 會被瀏覽器完整下載執行，任何人打開瀏覽器開發者工具都看得到。這是**純前端靜態網站的固有限制**，不是程式碼寫錯——JavaScript 本來就一定會被使用者的瀏覽器完整下載，沒有例外。

實際影響：
- 別人可以複製這把金鑰去發送請求，消耗你在 TMDb 的請求額度。
- TMDb 的 API 金鑰不像 Google Maps API 金鑰那樣支援「綁定網域」白名單，沒辦法限制這把金鑰只能在你的網站上使用。

如果之後想徹底解決（讓金鑰完全不出現在任何瀏覽器下載得到的檔案裡），需要在瀏覽器和 TMDb 中間加一層你自己控制的伺服器（例如 Cloudflare Workers、Vercel Serverless Function），把 `app.js` 裡直接呼叫 TMDb 的地方改成呼叫「你自己的網址」，金鑰改放在那層伺服器的環境變數裡，由伺服器代為夾帶金鑰去呼叫 TMDb。這是一項額外的基礎設施決定，目前這個純靜態版本沒有做，如果之後金鑰真的被濫用（TMDb 後台可以看到異常的請求量），第一步是去 TMDb 帳號設定重新產生一把新金鑰。

### 已經做了的防護

- **Content-Security-Policy**：`index.html` 的 `<meta http-equiv="Content-Security-Policy">` 限制頁面只能載入名單內的來源（Tailwind CDN、Google Fonts、FontAwesome、TMDb API/圖片），降低被注入惡意腳本的風險。有兩個先天限制寫在該處的註解裡（`style-src` 需要 `'unsafe-inline'` 是因為 Tailwind CDN 動態插入樣式；`frame-ancestors` 透過 `<meta>` 設定瀏覽器會忽略，只有伺服器回應標頭才真正生效，GitHub Pages 不開放自訂標頭）。
- **沒有使用 `innerHTML`**：所有從 TMDb 拿到的電影資料（片名、簡介、演員名單等）都是用 `.textContent` 寫入畫面，不是 `.innerHTML`，就算 TMDb 回傳的資料裡混入了 `<script>` 之類的內容，也只會被當成純文字顯示，不會被瀏覽器當程式碼執行。
- **沒有 inline 事件屬性**：原本圖片載入失敗的備用圖邏輯是寫在 HTML 的 `onerror="..."` 屬性裡，已經改成在 `app.js` 用 `addEventListener` 註冊，這樣 CSP 的 `script-src` 才能不需要放寬 `'unsafe-inline'` 就完整生效。
