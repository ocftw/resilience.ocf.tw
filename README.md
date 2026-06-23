# resilience.ocf.tw

公開網站：[https://resilience.ocf.tw/](https://resilience.ocf.tw/)

本 repo 是 **GitHub Pages 網站殼層**：負責自訂網域、根路徑轉址、404 處理，並透過 submodule 掛載實際網站內容。檢測、建置、研究報告的詳細說明請見下方連結的各上游專案。

---

## 本 repo 負責什麼

| 項目     | 說明                                                                   |
| -------- | ---------------------------------------------------------------------- |
| 部署分支 | `gh-pages`（GitHub Pages）                                             |
| 自訂網域 | `CNAME` → `resilience.ocf.tw`                                         |
| 根路徑   | `index.html` 轉址至 `/web`                                             |
| 404      | `404.html`：將 `/web/...` 錯誤路徑轉為 `/web/?url=...` 查詢參數        |
| SEO      | 根目錄 `robots.txt` 指向 sitemap                                       |
| 網站內容 | submodule `web/`（來自 [web-resilience-test-profile](https://github.com/irvin/web-resilience-test-profile) 的 `gh-pages` 分支） |

本 repo **不**執行檢測或靜態頁建置；內容更新流程請見 [跨專案上線流程](#跨專案上線流程)。

---

## 對外 URL 結構

| URL                | 內容                                                              |
| ------------------ | ----------------------------------------------------------------- |
| `/`                | 轉址至 `/web`（即 `web/index.html`）                              |
| `/web/`            | 首頁：網址搜尋、整體統計圖表                                      |
| `/web/<domain>/`   | 單一網站檢測結果（靜態頁，約 1800+ 網域）                         |
| `/web/report/`     | 研究方法與彙整報告（來自 `web-resilience-test` 的 `report` 分支） |
| `/web/sitemap.xml` | Sitemap                                                           |

---

## Repo 與 submodule 結構

```
resilience.ocf.tw/          ← 本 repo（網站殼層）
├── index.html, 404.html, CNAME, robots.txt, favicon.ico
└── web/                    ← submodule: web-resilience-test-profile @ gh-pages
    ├── index.html, app.js, statistic.*.tsv, sitemap.xml, …
    ├── <domain>/index.html （各站靜態頁）
    └── report/             ← 嵌套 submodule: web-resilience-test @ report
```

| Submodule 名稱                | 路徑          | 來源 repo                                                                                 | 分支       |
| ----------------------------- | ------------- | ----------------------------------------------------------------------------------------- | ---------- |
| `web-resilience-test-profile` | `web/`        | [irvin/web-resilience-test-profile](https://github.com/irvin/web-resilience-test-profile) | `gh-pages` |
| `report`（嵌套於 `web/`）     | `web/report/` | [irvin/web-resilience-test](https://github.com/irvin/web-resilience-test)                 | `report`   |

---

## 跨專案上線流程

從「新增／更新一個網站」到「在 resilience.ocf.tw 看到頁面」的完整步驟，請直接閱讀：

- **[web-resilience-test-profile/add-new-sites.zh-TW.md](https://github.com/irvin/web-resilience-test-profile/blob/main/add-new-sites.zh-TW.md)**
- 英文版：[add-new-sites.md](https://github.com/irvin/web-resilience-test-profile/blob/main/add-new-sites.md)

---

## 本 repo 操作

上游已完成 `web-resilience-test-profile` 的 `npm run deploy` 後，在本 repo 執行：

```bash
git submodule update --remote web-resilience-test-profile
git add web
git commit -m "update web submodule"
git push
```

> submodule 名稱為 `web-resilience-test-profile`，實際目錄為 `web/`。

發布後建議（詳見 [add-new-sites.zh-TW.md §5](https://github.com/irvin/web-resilience-test-profile/blob/main/add-new-sites.zh-TW.md)）：

- 確認 [GitHub Actions workflow](https://github.com/ocftw/resilience.ocf.tw/actions/workflows/gh-pages.yml) 已完成
- 依 [TESTING.zh-TW.md](https://github.com/irvin/web-resilience-test-profile/blob/main/TESTING.zh-TW.md) 做回歸測試（含 §3-2 的 `404.html` 行為）
- 若使用 Cloudflare「cache everything」，可手動 purge `resilience.ocf.tw` hostname
- 使用者端網站清單可能因 `statistic.tsv` 在瀏覽器 localStorage 快取 **24 小時**而延遲更新（見上游文件 §5-3）

---

## 授權

本網站所呈現之檢測資料與衍生頁面，在 ISIF 研究專案期間（2026 年 12 月 31 日前）採用 [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/)（姓名標示─非商業性─禁止改作 4.0 國際）。2026 年 12 月 31 日後將釋出至公有領域（Public Domain）。

完整條款與建議署名格式請見 [LICENSE](LICENSE)。
