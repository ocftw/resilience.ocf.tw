## resilience.ocf.tw

這個 repo 用來承載公開網站 `https://resilience.ocf.tw/` 的內容，  
目前主要透過 Git submodule 方式，引用其他兩個專案產出的結果：

- `web-resilience-test`（或同等的測試結果 repo）：提供 `statistic.tsv` 與各網站 JSON 測試結果
- `web-resilience-test-profile`：把測試結果轉成靜態頁面並部署到 `gh-pages`

> 簡單來說：  
> **測試與統計** 在 `web-resilience-test`，  
> **靜態頁面與部署** 在 `web-resilience-test-profile`，  
> **這個 repo 則作為實際對外服務的整體網站容器。**

---

## 從「新增一個測試網站」到「在 resilience.ocf.tw 上看到頁面」的完整流程

這裡整理一條龍流程，方便從這個 repo 追到上游專案。

### 1. 在 `web-resilience-test` 新增或更新網站測試結果

在 `web-resilience-test` 專案中：

1. 執行單一網站檢測並儲存結果：

   ```bash
   cd ../web-resilience-test
   npm run check --save https://www.example.com
   ```

2. 重新產生統計檔：

   ```bash
   node generate_statistic.js
   ```

3. 如果 `test-results/` 是獨立 submodule，記得在該目錄內 commit / push：

   ```bash
   cd test-results
   git add .
   git commit -m "新增網站測試結果: example.com"
   git push
   ```

### 2. 在 `web-resilience-test-profile` 重新建置靜態頁面

在 `web-resilience-test-profile` 專案中：

1. 更新測試結果 submodule：

   ```bash
   cd ../web-resilience-test-profile
   git submodule update --remote test-result
   ```

2. 建置新網站（或全部）對應的靜態頁面：

   ```bash
   # 只建 example.com 相關頁面
   npm run build example.com

   # 或建置全部網站
   npm run build:all
   ```

3. 將建置結果部署到 `gh-pages` 分支：

   ```bash
   npm run deploy
   ```

### 3. 更新本 repo 的 submodule 指向

```bash
cd ../resilience.ocf.tw
git submodule update --remote web-resilience-test-profile
git add .
git commit -m "update web submodule"
git push
```

部署完成後，`web-resilience-test-profile` 的 `gh-pages` 內容會被這個 repo 以 submodule 或對應設定方式掛載，  
對外即對應到 `https://resilience.ocf.tw/web/` 與 `https://resilience.ocf.tw/web/<domain>/` 等路徑。

---

## 常見檢查清單

- **新增網站後沒有出現在網頁：**
  - 確認在 `web-resilience-test` 有執行 `npm run check --save` 並重跑 `node generate_statistic.js`
  - 確認 `test-results` submodule 有 commit / push
  - 確認 `web-resilience-test-profile` 有更新 `test-result` submodule、重新 `npm run build` 或 `npm run build:all`，並 `npm run deploy`
  - 確認本 repo 有正確指向最新的 `gh-pages` / 對應 submodule 版本

- **Sitemap 沒更新或只剩少數頁面：**
  - 確認是用 `npm run build:all` 或 `npm run generate:sitemap` 在 `web-resilience-test-profile` 中重建 sitemap

---

## 相關 repo 一覽

- 測試工具與結果產生：`web-resilience-test`
- 靜態頁面生成與部署：`web-resilience-test-profile`
- 對外網站容器（本 repo）：`resilience.ocf.tw`

---

## 📜 授權

本網站所呈現之檢測資料與衍生頁面，在 ISIF 研究專案期間（2026 年 12 月 31 日前）採用 [CC BY-NC-ND 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/)（姓名標示─非商業性─禁止改作 4.0 國際）。2026 年 12 月 31 日後將釋出至公有領域（Public Domain）。

完整條款與建議署名格式請見 [LICENSE](LICENSE)。
