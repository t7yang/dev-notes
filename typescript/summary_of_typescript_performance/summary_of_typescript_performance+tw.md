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

> Preferring Interfaces Over Intersections

理由：

當合併多個型別時，建議先使用 `interface` ，因為 intersection 型別的方式會遞迴式的合併型別的屬性而 interface 則是建議一個扁平的物件型別。

> Using Type Annotations

理由：

自動推導的匿名型別可能成本極大，尤其對於函數的回傳型別。

> Preferring Base Types Over Unions

理由：

一個龐大的 union types 可能對編譯帶來極大的影響，一個替代的作法是用一個 base types 集合數個 subtypes 的共同屬性，譬如 `HtmlElemnt` 和 `DivElement ... ImgElement` 。

### Using Project References

理由：

把龐大的程式碼用 project references 的方式切割成數個獨立的專案有助於避免單一次編譯時載入太多檔案，同時也更容易組織不同策略的專案架構。 ([範例](https://dev.to/t7yang/typescript-yarn-workspace-monorepo-1pao))

### 使用 Project References

> Specifying Files

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

> Specifying Files

理由：

把「笨重」的資料夾如 `node_modules` 排除並且只包含（編譯）必要的資料夾，這樣可以避免編譯時因為存取不相關的資料夾而慢下來。所以請擇一設定 `files` 或 `include` 及 `exclude` 。

最好的方式是只包含有關的程式碼檔案，然後排除任何諸如資源、測試、相依套件的檔案資料家。

> Controlling `@types` Inclusion

理由：

TypeScript 自動包含所有的在 `node_modules` 內的 `@types` 套件，這會拖慢編譯及編輯的過程。

可以透過把 `types` 欄位設定為空陣列，然後手動加入必要的型別套件。

> Incremental Project Emit

理由：

啟用 `--incremental` flag 可以讓 TypeScript 保留上一次編譯的狀態進而幫助 TypeScript 只針對最少部分的檔案從新檢查或重新輸出。對透過使用 `composite` 的 project references 預設就會有上述增益的效果。

> Skipping `d.ts` Checking

理由：

TypeScript 會重新檢查所有的 `d.ts` 檔，縱然這通常是不必要的。 `skipDefaultLibCheck` 可以在邊一時跳過檢查由 TypeScript 所攜帶的 `d.ts` 檔，而 `skipLibCheck` 更是可以跳過檢查所有的 `d.ts` 檔。

> Using Faster Variance Checks

理由：

在沒有 `--strictFunctionType` 的情況下，具型別的參數是透過結構、逐個成員進行比對的。這牽扯到 variance 的概念，欲知詳情可閱讀 [tsconfig](https://www.typescriptlang.org/tsconfig#strictFunctionTypes) 及 [handbook](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html).

### 組態其他建構工具

TypeScript 可以跟其他建置工具一起使用，請詳閱所選擇的工具相關的效能說明。

> Concurrent Type-Checking

理由：

某些工具如 `fork-ts-checker-webpack-plugin` 或 `awesome-typescript-loader` 提供以獨立處理程序執行型別檢查的功能來降低等待時間。

> Isolated File Emit

理由：

諸如 `const enum` 及 `namespace` 的功能需要在產出前先檢查其他的檔案，這會拖慢輸出甚至是造成跟某些工具如 babel 的衝突。 `isolatedModules` 可以對上述這類不支援的功能進行提示。

### 調查問題

> Disabling Editor Plugins

理由：

編輯器的使用體驗可能會受到套件的影響。所以如果你遭遇任何問題，請先嘗試停用套件或閱讀套件的說明。

> `extendedDiagnotics`

理由：

如果編譯的時間超乎你的期待，嘗試以 `--extendedDiagnotics` 的方式執行來印出編譯器耗時的相關資訊。針對每一個欄位的涵義請查閱[這裡](https://github.com/microsoft/TypeScript/wiki/Performance#extendeddiagnostics)。

> `showConfig`

理由：

`tsconfig.json` 是可以延伸自其他組態檔， `showConfig` 會把最終合併的結果印出。

> `traceResolution`

理由：

透過 `traceResolution` 可以把編譯時會涵蓋的每一個檔案詳盡的輸出。如果發現任何不應該包含在內的檔案出現，請重新檢視 `include` / `exclude` 的設定或進一步檢視諸如 `types` 、 `typeRoots` 、 `paths` 。

> Running `tsc` Alone

理由：

TypeScript 跟第三方工具一起使用時可能會有較慢的表現。確保比較兩者之間的診斷資訊和組態是否有出入。

> Upgrading Dependencies

理由：

有些時候 TypeScript 會受到 `.d.ts` 型別檢查密集運算的影響。雖不常見，但仍可能發生。升級到最新版的 TypeScript 或最新般的 `@types` 套件通常可以解決這類問題。

### 常見問題

> Misconfigured `include` and `exclude`

針對 `include` 及 `exclude` 的建議設定。

```json
{
  // 只包含來源資料夾
  "include": ["src"],
  // 避免深層 node_modules 及 點檔案
  "exclude": ["**/node_modules", "**/.*/"]
}
```

### 填寫問題回報

如果你的專案已經進行正確且最佳的組態仍遭遇問題，你可以選擇填寫[問題回報](https://github.com/microsoft/TypeScript/issues/new/choose)。

在回報問題前你可以先遵循下列步驟：

- 先在 nightly 版本測試你的問題，確保該問題不是已經被解決的。
- 透過 `--trace-ic` 、 `generateCpuProfile` 等 flag 先對編譯器進行編譯的建檔。
- 如果是編輯時的效能問題，請先蒐集 Visual Studio Code 的 TSServer 紀錄。
