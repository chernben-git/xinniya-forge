# 新爾雅 · 生文（Corpus Forge）

台語職業敘事語料生成介面。純靜態單檔，掛 GitHub Pages 免主機。
真正生成佇你家己的 Corpus API（本頁只是殼，金鑰使用者家己填，存佇瀏覽器 localStorage，**無寫死佇碼內**）。

---

## 一、部署（GitHub Pages）

1. 新開一个 repo（例：`xinniya-forge`）
2. 共 `index.html` 佮這份 `README.md` 傳上去（根目錄）
3. Settings → Pages → Source 揀 `Deploy from a branch` → `main` / `(root)` → Save
4. 一兩分鐘後得著網址：`https://<帳號>.github.io/xinniya-forge/`

## 二、API 端愛先做的兩件（無做網頁一定袂通）

### 1. HTTPS（Cloudflare Tunnel）
Pages 是 https，若 API Base URL 是 `http://localhost:3777`，瀏覽器會直接擋（混合內容，CORS patch 救無）。
先起 tunnel 提著 `https://xxx.trycloudflare.com`，網頁「設定」內填彼个。

### 2. CORS patch（`corpus_api_v1.js`）

`json()` 內的 `writeHead`：
```js
res.writeHead(code, {
  'Content-Type': 'application/json; charset=utf-8',
  'Access-Control-Allow-Origin': 'https://<帳號>.github.io',  // 建議鎖網域，毋是 *
  'Access-Control-Allow-Headers': 'x-api-key, Content-Type'
});
```

`http.createServer` 內第一逝加 preflight：
```js
if (req.method === 'OPTIONS') {
  res.writeHead(204, {
    'Access-Control-Allow-Origin': 'https://<帳號>.github.io',
    'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
    'Access-Control-Allow-Headers': 'x-api-key, Content-Type'
  });
  return res.end();
}
```
改了重跑 `node corpus_api_v1.js`。

## 三、使用
開網頁 → 右頂「設定」→ 填 API Base URL（tunnel 網址）＋金鑰 → 存 → 拍職業名生成。

## 四、功能
- **文章生成**：職業＋篇數（1/3/5/10，單次上限 10）→ 卡片顯示，逐篇字數（codepoint 計，𤆬𨑨算一字）／抄鈕／對照鈕
- **對照面板**：骨架已入，預設收合；台⇄華／台⇄英 分頁鈕已排，管線接落去只換內容
- **句子生成／改文**：分頁佔位（掛帳），API 有了直接開
- **今日用量**：頂列拍 `/quota`

## 五、規矩（防蒸餾）
- 單次上限 10 篇（前端已 clamp）
- 429/500 就停，**無自動重試**，予使用者手動閣試
- admin 金鑰**毋通**寫入 repo 抑是任何公開碼
