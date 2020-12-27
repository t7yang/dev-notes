# TypeScript 效能摘要

這是 TypeScript 官方有關[效能的文章](https://github.com/microsoft/TypeScript/wiki/Performance)摘要。

### 懶人包

- 優先使用 `inteface` 而不是 `type` 作為合併多個型別及用一個 base types 和多個 subtypes 的方式來取代大型的 union types 。
- 主動標示型別尤其是回傳的型別。
- 龐雜的程式量建議使用 project references 。
- 正確的在 `tsconfig` 或 `jsconfig` 中設定 `include` 及 `exclude` 。
- 啟用一些有助於提升速度的選項： `incremental`、`skipLibCheck`、`strictFunctionType`、`isolatedModules` 。
- 如果有使用其他工具，請詳閱相關工具的效能說明。
- 調查問題的方式有停用編輯器的外掛、以 diagnotics 及 trace 的方式執行 `tsc` 。

### 撰寫易於編譯的程式碼

> Interfaces 優先於 Intersections

理由：

當合併多個型別時，建議先使用 `interface` ，因為 intersection 型別的方式會遞迴式的合併型別的屬性而 interface 則是建議一個扁平的物件型別。

> 標示型別

理由：

自動推導的匿名型別可能成本極大，尤其對於函數的回傳型別。

> Base Types 優先於 Unions

理由：

一個龐大的 union types 可能對編譯帶來極大的影響，一個替代的作法是用一個 base types 集合數個 subtypes 的共同屬性，譬如 `HtmlElemnt` 和 `DivElement ... ImgElement` 。

### Using Project References

理由：

把龐大的程式碼用 project references 的方式切割成數個獨立的專案有助於避免單一次編譯時載入太多檔案，同時也更容易組織不同策略的專案架構。 ([範例](https://dev.to/t7yang/typescript-yarn-workspace-monorepo-1pao))

### 使用 Project References

> 指定檔案

理由：

排除「沉重」的資料夾如 `node_modules` 及只包含需要被編譯的檔案可以有效避免編譯時存取非必要的資料夾，否則會減慢編譯的速度。正確的設定 `files` 或是 `include` 及 `exclude` 可以有效規避上述的情況。

最佳實踐是只涵蓋那些需要被編譯的程式碼資料夾並排除不相關的資源、測試、相依套件的檔案及資料夾。

> Controlling `@types` Inclusion

理由：

TypeScript 會自動納入每一個在 `node_modules` 裡面的 `@types` 套件，這會讓編輯和編譯的速度下降。

透過指定 `types` 欄位為空陣列並手動加入必要的型別套件可以改善這個問題。

> Incremental Project Emit

理由：

`--incremental` flag 可以讓 TypeScript 儲存上一次編譯的狀況並幫助 TypeScript 對最少的檔案進行重新檢查或觸發更新。使用 project references 時開啟 `composite` flag 也會預設啟用增益的效果。

> Skipping `d.ts` Checking

理由：

雖然通常來說不必要，但 TypeScript 會全面對 `.d.ts` 檔案重新檢查。 `skipDefaultLibCheck` 可以跳過檢查 TypeScript 自帶的 `.d.ts` 檔，而 `skipLibCheck` 還可以進一步在編譯時跳過所有 `.d.ts` 的檢查。

> Using Faster Variance Checks

理由：

若不啟用 `--strictFunctionType` ，標註型別的參數會以結構的方式對每一個成員進行檢查。 對這個議題有興趣可以閱讀 [tsconfig](https://www.typescriptlang.org/tsconfig#strictFunctionTypes) 及 [handbook](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html).

### 組態 `tsconfig` 或 `jsconfig`

### 組態其他建構工具

### 調查問題

### 常見問題

### 填寫問題回報
