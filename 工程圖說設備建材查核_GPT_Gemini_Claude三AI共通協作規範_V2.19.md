# 工程圖說設備建材查核－GPT × Gemini × Claude 三 AI 共通協作規範
**版本：V2.19**  
**日期：2026-09-25**  
**適用：台灣營造工程各案件之工程圖說、規範、標單、設備與建材查核。**  
**前版：V2.18**  
**前版 SHA-256：`c81a852ade5a319d18a38e8c1090f3335ed062fe64abf105047d8c293efa0869`**  
**本版核心：新增 `Adjacent Batch Combined Run｜相鄰 Batch 合併執行` 的最小治理語意。保留既有每 5 個 Material-ID 的正式 Batch-ID 與歷史，不重編 Master Register；允許兩個相鄰既有 Batch（最多 10 項）以一次 Combined Run 合併 Gemini R1、GPT Final、Claude-FA Final Audit、GPT-W Excel Execution 與 Formal Completion。Combined Run 內部固定維持 `Segment A＝前 5 項`、`Segment B＝後 5 項`，兩 Segment 的 Related-Page Source Discovery、原圖影像、Necessary Requirements、Candidate Evidence、P/U/F、RFI、MCR、Source Grounding 均獨立，不得跨材料拼證。新增 `Combined Run Safety Split`：若 10 項工作包或 Final Audit／Excel 執行規模已影響材料邊界、來源完整性、Claude Token／工具負荷或 Excel 結構安全，退回原兩個 5 項 Batch 個別完成；此非 Reserve／Failover。本版不新增 Batch-ID、Gate 編號、AI 角色、Executor、Excel 工作頁／欄位／評分／公式，不追溯重開已進行或已封版 Batch。**

---

# 0. 規範優先順序

1. 使用者最新明確指示
2. 正式圖說／契約／特訂規範／設計監造正式文件／Current Formal Excel Baseline（依第二十四節解析）
3. 本共通規範 V2.19
4. 各 AI 專屬 Instructions
5. 當前材料最新「單一權威 MD」
6. 歷史 MD、舊交接檔、舊摘要、已被新版取代之文件

工程技術事實始終以正式原始證據為最高依據。新版規則不得用來覆蓋原圖、契約或正式核定文件。

---

# 一、三 AI 固定角色

## 1.1 GPT＝技術主責與最終整合者

GPT 負責：
- 每材料重新查看指定原圖
- 技術需求與必要條件拆解
- 候選品牌／製造商／確切型號研究
- 證據強度判斷
- P／U1／U2／U3／F／N/A
- 判定理由與證據定位
- RFI／缺件／疑似限制競爭
- 與 Gemini 最多兩輪內收斂
- 自檢①／②／③
- 最終技術定稿
- 產生唯一 `GPT→Final Auditor 最終工作包`；Normal／Reserve High Risk 送 Claude-E，Reserve／Failover Low Risk 送 Gemini-FA
- Reserve／Failover Mode下，依 Claude Mandatory Review Checklist（見第二十一節）客觀判斷是否命中必審條件，不得以主觀信心自行認定「低風險」
- 每次輸出前執行第二十二節「來源可追溯與零腦補」檢查，主動找出任何無法追溯之數據、單價、規格、品牌、型號、CNS、數量或技術敘述

一般對話模式 GPT 不生成、修改、驗證或升版正式 Excel；正式 workbook 由 GPT-W／ChatGPT Work 執行。一般對話 GPT 只負責技術整合、Excel Execution Package 與必要 Drive Seal Relay。

## 1.2 Gemini＝前段獨立交叉複核者

Gemini 負責：
- Round 1 獨立重看原圖並全面挑錯
- Round 2 僅處理尚未閉合差異
- 對必要條件、候選身分、P/U/F、RFI、疑似限制競爭提供獨立意見
- 直接形成可寫入 V8 的技術判定，不得假設 Excel 後續會替技術鏈重新判定 P/U/F
- Reserve／Failover Mode下，依第二十一節可擔任 `Gemini-FA＝Gemini Final Auditor`，執行 Cold Audit
- 對 GPT 提供之數據、單價、規格與技術結論執行第二十二節之獨立反腦補複核，不得因敘述看似合理就視為有來源

Gemini 不寫 Excel，也不取代 GPT 最終裁決。

## 1.3 Claude＝Final Auditor only

自 V2.14 起，Normal Mode 正式角色改為：

`Claude-FA＝Final Auditor`

為相容歷史 Stage Chain，舊文件中的 `Claude-E` 若描述 Final Audit，可視為 `Claude-FA` 的歷史識別；但 **不得再據此推論 Claude 是 Normal Mode 的 Excel Executor**。

Claude-FA 收到 GPT 最終工作包後只負責：
1. 做最後一次高風險獨立技術審核；
2. 核對原圖重大錯讀、必要條件漏／重、Candidate-ID／型號、P/F 證據對稱、跨型號／跨系列／跨證書、RFI／限制競爭等會改變技術答案的重大風險；
3. 無重大錯誤回 `Final Audit＝PASS`；
4. 有重大錯誤只回 `Excel Write Blocker｜Final Audit Failed` 與必要 Blocker。

Normal Mode 中 Claude-FA：
- **不建立／修改正式 Excel candidate**；
- **不執行 Excel 公式／recalc／V0～V8／Drive Seal**；
- **不因 Excel 寫入而消耗工具額度或長上下文**；
- PASS 後即退出當輪正常技術鏈，後續 Excel 由 GPT-W／ChatGPT Work 執行。

Reserve／Failover Mode 下 Claude 的 Final Audit／QC 責任仍依第二十一節；不因取消 Normal Mode Excel 寫入而降低第三模型品質控制。

## 1.4 GPT-W／ChatGPT Work＝Default Excel Executor

自 V2.14 起，Normal Mode 標準日常材料更新與 Deferred Excel Batch 的預設正式 Excel Executor 為：

`GPT-W／ChatGPT Work`

GPT-W／ChatGPT Work 作為 Normal Mode 預設 Executor 的**角色指定**屬專案層級常態設定；此角色指定不等於任何特定 Excel 寫入任務的 `User Execution Authorization`。

每一個要實際建立／修改正式 Excel candidate 的執行任務，GPT-W 在第一次寫入前仍須取得使用者一次明確執行授權。Execution Package 本身只證明「技術與執行規格已 READY」，不得視為使用者已授權修改 workbook。

使用者一旦已明確授權，且授權內容可唯一對應：
- Execution Base／Current Formal Excel Baseline；
- Execution Package；
- 執行範圍；

即視為該次 Excel 執行已完成唯一一次啟動確認。GPT-W 不得再要求第二次「是否開始」或語意等價確認，應直接進入既有 Execution Capacity Preflight 與後續執行鏈。只有要**改成其他 Executor**時，仍須使用者另行明確授權。

Claude-FA Final Audit PASS 後：
- GPT 固定 Final Audit PASS 識別、Current Formal Excel Baseline／SHA、預定輸出版本；
- GPT 建立 `Excel Execution Package／Excel Write Payload`；
- GPT-W 僅依已固定技術結果機械寫入，不重新研究或改判 P/U/F；
- GPT-W 執行必要 recalc、V0～V8、V8-L；若 Work 可安全完成 Drive raw-byte 上傳／回讀，直接完成 V8-D；否則由一般 GPT 只做 Drive Seal Relay；
- 同一預定輸出版本只有 GPT-W 可修改 workbook；Claude-FA 不得對同版 Excel 寫入。

若 GPT-W／Work 當下不可用：
`Excel Execution Deferred｜GPT-W Unavailable`

不得自動把 Excel 工作轉回 Claude。只有使用者明確指定 `Claude Excel Fallback＝Enabled` 後，才可依既有 Planned-Checkpoint／Resume／V0～V8 規則由 Claude 擔任該特定版本唯一 Executor。

### 1.4.1 V2.14 生效時之既有 Claude 半成品轉換

若 V2.14 生效時，某預定版本已存在由 Claude 寫入但尚未 V8 Formal Seal 的 candidate／checkpoint：
- **GPT-W 不得接續修改該 Claude candidate**，避免同一版本跨 Executor 競寫；
- 該檔可保留作唯讀差異／取證參考，但不得成為 GPT-W Execution Base；
- GPT-W 必須從當下合法 `Current Formal Excel Baseline` 建立全新 candidate；
- GPT 以已固定 Final Work Package／Batch Manifest／Claude Final Audit PASS 重新產生 Excel Execution Package；
- 只要技術內容與 Final Audit 基準未變，不重跑 Gemini、不重跑 Claude Final Audit；
- 原 Claude 半成品狀態記為 `Superseded by Executor Transition｜Formal Completion＝NO`。

此為一次 Executor Transition Rebuild，不是技術重開。
---

# 二、V8 責任分界

## 2.1 三 AI 技術鏈負責

三 AI 技術鏈必須在 Excel 寫入前完成：
- 原圖必要條件拆解
- 候選品牌／製造商／確切型號
- 證據強度
- P／U1／U2／U3／F／N/A
- 判定理由
- 證據來源與證據對應型號
- 證據 URL／文件
- RFI／缺件
- 疑似限制競爭
- 跨型號／跨系列／跨證書檢查

P／U1／U2／U3／F 是**AI 技術判斷的正式結果**，在進入 Excel 前即應固定。

## 2.2 Excel 負責

Excel 僅負責：
- 保存正式技術結果
- COUNTIF／COUNTIFS 類簡單統計
- 完成率
- A／B／C／D／E 機械式計算
- 唯一鍵
- 漏項／重複檢查
- 少量一致性驗證

Excel **不得依其他證據欄位再次推導或改寫 P／U1／U2／U3／F**。

---

# 三、V8 正式五工作頁架構

標準正式母版固定為五個**邏輯工作頁**：

1. `材料資格基準`
2. `供應商候選清單`
3. `RFI與缺件追蹤`
4. `疑似名單`
5. `查核明細與規則`

上述名稱為規範中的邏輯名稱；正式 Excel 實際頁籤得依母版帶有版本尾碼，例如 V8 母版可為 `材料資格基準V8`。Executor 必須以**指定安全母版的實際頁籤名稱**定位，不得為了讓名稱看起來一致而自行改名、刪除版本尾碼或重建工作頁。

正常材料更新不得自行新增第六頁。

若確有新增工作頁／欄位／功能必要：
1. 先提出必要性與影響；
2. 經使用者明確同意；
3. 才可修改正式架構。

## 3.1 `查核明細與規則` 最低內容

### A｜正式來源與判定規則
至少包含：
- 官方資料庫／正式來源索引
- P/U/F 定義
- A/B/C/D/E 規則
- 證據強度原則
- 禁止跨型號拼證

### B｜必要條件主檔
至少包含：
- 材料ID
- 細項ID
- 圖說位置
- 必要條件
- CNS／試驗方法
- 最低可接受證明
- 是否納入完成率

### C｜候選逐項判定
至少包含：
- Candidate-ID
- 材料ID
- 細項ID
- P/U1/U2/U3/F/N/A
- 判定理由
- 證據來源
- 證據對應型號
- 證據 URL／文件
- N/A 理由

### D｜Failover／Reserve與Claude QC留痕（V2.1新增）

僅於曾啟動 Reserve Mode 或 Failover Mode 之材料建立紀錄列，採極簡靜態欄位，不建立公式引擎：

- 材料ID
- Mode（Normal／Reserve／Failover／Safe Stop）
- Final Auditor（Claude-E／Gemini-FA）
- Executor（Claude-E／GPT-W）
- 觸發原因
- 使用者確認（`Failover Trigger＝User Confirmed` 或 Reserve啟動紀錄）
- 風險分類依據（`Mandatory Checklist＝全部否`，或具體 `MCR-xx`）
- Claude抽查狀態（未抽查／已抽查）
- 抽查結果

本子表細節規則見第二十一節，寫入原則為**寫入值，不新增公式或評分欄位**。

舊版工作表中仍有效的技術資料必須濃縮移入本頁或其他對應正式頁，不得因精簡工作簿而遺失。

---

# 四、原圖與證據硬規則

每個材料均須：
- GPT 在定 Necessary Requirements 前先完成 `Related Source Page Discovery`，不得把既有指定頁碼視為完整封閉集合；
- GPT 對所有命中頁重新查看原始頁面影像，建立 `Related Source Page Set` 並標示 Include／Exclude；
- Gemini Round 1 將 GPT 提供之 Source Page List 視為起始集合，必須獨立再做一次 Related Source Page Discovery；
- Gemini 對新命中頁重新查看原始頁面影像並獨立判 Include／Exclude；
- Claude-E 最後審核仍只重新查看 GPT 最終工作包指定之高風險原圖關鍵頁；本 Gate 不要求 Claude 重跑完整 Related Source Page Discovery。

不得只依：
- OCR
- 舊摘要
- 歷史 MD
- 記憶
- 搜尋摘要

取代原圖確認。

證據優先原則：
正式原圖／契約／特訂規範／正式補充文件 > 原廠正式型錄或證書 > CNS／政府／認證資料 > 原廠網站 > 正式代理資料 > 一般網頁 > 搜尋摘要。

搜尋摘要不得單獨作為 P 或 F 的充分證據。

## 4.1 Related-Page Source Coverage Gate｜關聯原圖頁覆蓋閘門

本 Gate 為既有原圖查核與 Source Grounding 的前段覆蓋子閘門，不新增 AI 技術節點。

### 4.1.1 GPT 第一責任

每一材料／設備在固定 Necessary Requirements 前，GPT 必須先執行 `Related Source Page Discovery`。既有工作包、材料表或前一輪已列頁碼只能作為**起始集合**，不得直接視為完整集合。

搜尋索引至少包含：
- 材料名稱與合理同義詞；
- Material-ID／圖號／設備代號；
- 已知 CNS／標準號；
- 關鍵性能詞／試驗方法；
- 材料表／設備表；
- 工程材料試驗彙整表；
- 一般說明；
- 施工規範／大樣圖；
- 交互引用頁。

OCR、PDF文字層、全文搜尋、索引或程式化文字掃描只可用於**頁面定位與候選頁發現**，不得取代原始頁面影像判讀。

所有命中頁均須重新打開原始頁面影像確認，並建立：
`來源檔｜頁碼／圖號｜命中原因｜Material-ID｜Include/Exclude｜排除理由`

`Exclude` 不得留白。遇到「非屬本工程」「建議施作」「其他系統／材料」「僅為通用說明但未指向本材料」等情況，必須留下具體排除理由，不得靜默納入 Necessary Requirements。

GPT 完成後固定：
`Related-Page Coverage＝PASS`

### 4.1.2 Gemini 獨立第二防線

Gemini R1 不得只重看 GPT 已列頁面。GPT Source Page List 只作為起始集合；Gemini 必須自行以材料名／同義詞／CNS／代碼／關鍵性能詞等至少做一次獨立 Related Source Page Discovery。

Gemini 發現 GPT 未列頁時：
1. 重新打開該原始頁面影像；
2. 判斷 Include／Exclude；
3. 說明新增頁對 Necessary Requirements／P-U-F／RFI 是否有影響；
4. 若 Exclude，寫明排除理由。

Gemini 未完成 `Related-Page Coverage＝PASS` 前，不得標示：
`R1 Status＝Closed`

R1 發現漏頁應在 R1 直接處理；只有仍存在既有制度定義之實質差異時才進 R2，不因此新增 R3。

### 4.1.3 Shared-Page Reuse 保留

本 Gate 新增的是「頁面發現與覆蓋責任」，不是要求同一頁重複載入。

同一 Batch 中 M06～M10 共用同一頁時，仍依既有 Shared-Page Visual Audit Deduplication／Canonical Crop Reuse：
- 同一來源頁可只載入一次；
- 同一清晰裁切可重用；
- 各材料技術判定仍獨立；
- 不得因 Coverage Gate 把同頁機械式重載多次。

### 4.1.4 Claude Token 零增量

Related-Page Source Coverage Gate 的完整發現成本由 GPT＋Gemini 前段承擔，不新增 Claude 工作量：
- 不新增 Claude MCR；
- 不提高 Claude QC 抽樣率；
- 不要求 Claude 重跑完整 Related Source Page Discovery；
- 不要求 Claude 讀取全部 Include／Exclude 歷史表；
- Claude 仍依既有 Final Audit Scope Lock 查高風險關鍵頁。


---

# 五、必要條件拆解

原則：
**一個可獨立驗證的義務＝一個必要條件。**

若圖說同時明列：
- 整體產品須符合某 CNS；
- 個別性能門檻；
- 尺寸／厚度／材質；
- 試驗方法；
- 證書或標章；

應依「是否可獨立驗證」拆分，不得為了提高完成率而任意合併。

若「整體 CNS 符合性」與個別性能同時明文存在，除非正式標準或案件文件明確證明前者僅為試驗方法引用，否則應保持可獨立追蹤。

---

# 六、P／U1／U2／U3／F／N/A

- **P**：已有達到最低可接受證據強度之資料，且結果符合必要條件。
- **F**：已有與 P 相同最低證據強度之資料，且結果明確不符合必要條件。
- **U1**：尚未取得可用證據。
- **U2**：已有資料，但證據強度不足、型號未閉合或資料不足以判 P/F。
- **U3**：存在同級或更高強度證據衝突，需外部釐清。
- **N/A**：該條件對此候選確實不適用，且必須有可追溯理由。

## 6.1 P/F 證據強度對稱

P 與 F 採相同最低證據強度。

不得：
- 對有利資料用低門檻判 P、對不利資料要求高門檻才 F；
- 或反向操作。

廠商未公開資料不等於 F；原則應依情況落入 U1/U2。

---

# 七、Candidate-ID 與禁止拼證

每個 Candidate-ID 必須：
- 對應單一確切型號；或
- 明確標示為系列／家族級候選。

系列級候選不得把不同型號的不同證據拼成一個確切產品 P。

需要確切型號對應的證據，例如：
- 環保標章
- CNS 完整試驗
- 性能報告
- 尺寸／厚度
- 構造證明
- 證書效期與適用範圍

一律不得跨型號、跨系列、跨證書套用。

---

# 八、六節點封頂與多材料批次查核

## 8.1 標準流程

1. **GPT①**：Related Source Page Discovery＋原圖影像覆蓋＋查證＋初判＋自檢① → GPT→Gemini R1 單檔
2. **Gemini①**：獨立 Related Source Page Discovery＋原圖影像覆蓋＋全面複核 → Gemini→GPT R1 單檔
3. **GPT②**：逐項裁決、修正、形成剩餘差異 → GPT→Gemini R2 單檔
4. **Gemini②**：只處理剩餘差異 → Gemini→GPT R2 單檔
5. **GPT③**：最終裁決＋正式定稿＋自檢②③ → GPT→Claude-E 最終單檔
6. **Claude-FA**：最後高風險審核；PASS 後結束 Claude 任務，交 `GPT-W／ChatGPT Work` 依 Executor Package 執行 Excel＋V0～V8

若 Gemini R1 已完全收斂，可省略第4節點所需之 R2 往返，直接進 GPT 最終定稿。

第6節點於 Reserve／Failover Mode下之替代路徑見第二十一節。

### 8.1.1 Multi-Material Batch Review｜前段多材料批次查核

Normal Mode 的前段 GPT↔Gemini 技術鏈允許一份工作包同時包含多個材料／設備。

`Single-Handoff` 的「Single」係指：
1. 每一方向、每一技術輪只有一份當前權威輸入工作包；
2. 接收 AI 每一技術輪只產生一份當前權威輸出；
3. 不得同時存在多份彼此競爭的平行權威工作包。

**Single-Handoff 不代表一份工作包只能包含一個材料。**

例如 GPT 可建立：
`M36-M40_GPT_to_Gemini_R1_Batch_V1.md`

Gemini 應在同一 R1 中依序完成 M36～M40 的獨立複核，不得僅因舊有「每材料」文字或單材料檔名範例要求拆成五次獨立對話或五份工作包。

批次只改變交付與回覆單位，不改變逐材料技術義務。Batch 中每一材料仍須個別完成：
- Related Source Page Discovery；
- `Related Source Page Set` 建立與 Include／Exclude 判定；
- 所有命中頁原始圖說頁面影像重看；
- `Related-Page Coverage＝PASS`；
- 材料本體、圖號／代碼與適用範圍核對；
- 必要條件拆解；
- Candidate-ID／品牌／製造商／確切型號核對；
- P/U1/U2/U3/F/N/A；
- 判定理由、證據來源及證據對應型號；
- 跨型號／跨系列／跨證書檢查；
- RFI／缺件；
- 疑似限制競爭；
- MCR；
- 第二十二節來源可追溯與零腦補檢查。

不得以「同批相似材料」取代逐材料原圖重看或共用技術結論。

### 8.1.2 Per-Material Closure｜逐材料閉合

Batch R1 後，每一材料先固定 `Related-Page Coverage＝PASS／FAIL`，再獨立標示：
- `R1 Status＝Closed`
- `R1 Status＝R2 Required`
- `R1 Status＝Material Blocker`

`Related-Page Coverage≠PASS` 時不得標示 `R1 Status＝Closed`。

某材料進 R2，不使同批已 `Closed` 材料重新進 R2。

若只有特定材料輸入不足，原則僅標示：
`Material Blocker｜Mxx｜Input Incomplete`

除非缺件影響整批共同基礎證據，否則不得整批退回。

### 8.1.3 Batch R2｜只處理未閉合材料

例如 R1 結果為：
- M36＝Closed
- M37＝Closed
- M38＝R2 Required
- M39＝Closed
- M40＝R2 Required

則下一份 R2 Batch 僅包含 M38、M40 之尚未閉合差異。M36、M37、M39 不得重跑。

批次不得成為新增 Gemini R3、重做已閉合市場研究或降低 P/F 證據強度的理由。

### 8.1.4 Adjacent Batch Combined Run｜10項合併執行、內部5＋5

在不改變既有 Batch-ID 的前提下，允許兩個**相鄰既有 5 項 Batch**合併為一次 Combined Run；Combined Run 是執行／交付 overlay，不是新 Batch-ID 體系，也不是第五種 Mode。

例如：
- `B06＝M26～M30`
- `B07＝M31～M35`
- 合併執行識別：`B06＋B07 Combined Run｜M26～M35`

硬規則：
1. 原 B06、B07 的 Batch-ID、Material-ID、Master Register 與歷史狀態均不改名、不重編。
2. 一次 Combined Run 最多包含兩個相鄰既有 Batch，共 10 個 Material-ID。
3. 內部固定分：
   - `Segment A＝前一 Batch／前5項`
   - `Segment B＝後一 Batch／後5項`
4. Segment A、Segment B 各自完整執行 GPT①：
   `Related Source Page Discovery → 原始頁面影像 → Primary/Application/Cross-page/Exclude → Related-Page Coverage → Necessary Requirements → Candidate Research／Evidence → P/U1/U2/U3/F → RFI／疑似限制競爭 → GPT①`
5. Segment A 完成不得作為 Segment B 省略原圖、NR、Candidate Evidence 或 Source Grounding 的理由；Segment B 亦同。
6. 只有 A、B 均完成 GPT①後，才建立一份 `GPT→Gemini R1 Combined Batch Package`；內容必須以 `Batch → Material-ID → Candidate-ID → NR` 保持清楚邊界。
7. Gemini R1 原則一次處理完整 Combined Run，仍按原 Batch 與 Material-ID 分區；R2 規則完全不變，只處理實際未閉合之 Material-ID＋爭點，正常不得 R3。
8. GPT Final 可合併為一份 Combined Final，但每個原 Batch／Material-ID 的 Necessary Requirements、Candidate-ID、P/U/F、RFI、MCR、Source Grounding 與證據鏈仍須獨立可追溯。
9. Combined Final 送 Claude-FA 前，GPT 必須先完成既有 `Final Audit Visual Index`／Shared-Page 去重；不得把 Gemini R1/R2 歷史對話、完整市場研究或重複證據全文塞入 Claude 工作包。
10. Claude-FA 正常以一次 Multi-Material Final Audit Batch 審核 Combined Final；某一 Material-ID 發生 Blocker 時，原則只退回受影響 Material-ID／爭點，不自動重開其餘已閉合材料。
11. Claude-FA PASS 後可建立一份涵蓋兩個原 Batch 的 GPT-W Excel Execution Package，使用者一次正式授權即可啟動同一 candidate 的 10 項寫入；不得為兩個 Batch 建立互相競爭的 candidate。
12. Formal Completion 可用一份 Combined Completion 封裝，但必須分別記錄兩個原 Batch 的 `Formal Completion＝YES`，並固定同一個新的 `Current Formal Excel Baseline`。
13. 已開始技術鏈或已正式封版之既有 Batch，不因本規則追溯重開。Combined Run 僅對啟用後尚未進入既有 handoff／Final Audit 的相鄰 Batch 生效。

**Combined Run Safety Split｜安全退回5＋5：**
若出現任一情況，GPT 必須在不降低既有技術強度下，將 Combined Run 退回原兩個 5 項 Batch 個別完成：
- 任一 Segment Related-Page Coverage 無法閉合；
- 原圖／規格衝突大量增加；
- 10 項工作包規模已影響材料／候選／NR 邊界可追溯性；
- Claude-FA 即使套用 Shared-Page／Canonical Crop／Scope Lock 後，仍有明顯上下文或工具負荷風險；
- GPT-W Preflight 判斷 10 項一次寫入會提高結構或執行失敗風險。

固定留痕：
`Combined Run Safety Split｜原因`

Safety Split 不是 Reserve／Failover、不是 Targeted Reopen、不建立新 AI 角色／Gate／Batch-ID；已完成且仍有效的逐材料技術結論、Final Audit PASS 或正式證據不得無理由重做。

## 8.2 正常不得 Gemini Round 3

Round 2 後仍未閉合者：
- U1／U2／U3
- RFI／缺件
- F

由 GPT 最終裁決。

不得為追求所有項目均 P 或所有 AI 文字完全相同而無限討論。

## 8.3 Targeted Reopen 唯一例外

第二輪後若新取得先前不存在或未取得的正式權威證據，且足以推翻：
- 材料身分
- 必要條件數
- 確切型號
- P/F
- 疑似限制競爭核心結論

可啟動一次 Targeted Reopen，只處理該新證據。

SHA、排版、Excel 結構、流水號或一般 U1/U2 不得觸發額外 Gemini 技術輪次。

---

# 九、Single-Handoff 與跨 ChatGPT 對話續作

## 9.1 三 AI 正式 Single-Handoff｜Stage Chain

三 AI 正式 Single-Handoff 用於鎖定送審版本、責任邊界、SHA、技術結論與 Stage Chain，**不是單純為了讓 AI 記住前文**。每一方向、每一輪只傳一份當前權威 MD：

**本規範所稱「一份」是單一權威文件，不是單一材料限制；一份權威 MD 得包含多個材料／設備，只要各材料均可獨立追溯。**

- GPT→Gemini R1／R2：每輪 1 份
- Gemini→GPT R1／R2：每輪 1 份
- GPT→Final Auditor：1 份最終權威工作包
- Claude-E PASS：1 份正式驗證／Final Audit PASS 報告
- Claude-E FAIL：1 份 Blocker
- Gemini-FA PASS：1 份 `Final Audit PASS｜Fallback Auditor = Gemini-FA`
- Gemini-FA FAIL：1 份 `Excel Write Blocker｜Fallback Final Audit Failed`
- Final Auditor 與 Excel Executor 不同時：由 GPT 依已固定內容只建立 1 份 `Excel Executor 正式工作包`，引用已固定的 GPT 最終工作包與 Final Audit PASS；不得藉封裝重新研究或改寫 P/U/F
- Excel Executor 完成後：若 Executor 可直接完成 Drive raw-byte 封印，輸出 1 份 V0～V8 正式驗證報告；若 Executor 僅完成本地驗證，則先交付「本地封版候選檔＋SHA＋大小＋驗證摘要」，由 GPT 完成 Excel Drive Seal Relay 後固定最終 V0～V8 正式驗證報告。
- `Deferred Excel Batch` 啟用時：另建立 1 份版本化 `Batch Manifest` 作為批次控制索引；Manifest 不是第二份技術工作包，也不取代逐材料最終權威 MD。每次內容變更必須升 Manifest 版本並重算 SHA，批次正式封版前固定最終 Manifest SHA。

最新版單檔必須吸收仍有效結論與來源。不得要求下一 AI 同時接收多份歷史初判、歷史 Gemini 報告、舊高度共識檔、舊 Claude 指令或舊 Excel 清單。

專案基礎檔如原始工程圖、正式安全 Excel、契約、共通規範與各 AI Instructions 不計入「一份 MD」限制。Reserve／Failover Mode 下 `GPT→Gemini-FA` 仍受本節 Single-Handoff 原則約束。

## 9.2 跨 ChatGPT 對話續作｜非 Stage Chain

`ChatGPT 舊對話 → ChatGPT 新對話` 與上述三 AI 正式 Single-Handoff 是兩種不同機制。

跨 ChatGPT 對話續作原則：
1. 原則不再強制為了換聊天視窗而生成完整「新對話交接 MD」。
2. 新對話應優先自行讀取／恢復：
   - 最新正式共通規範與各 AI Instructions；
   - 最新正式 Excel 與驗證報告；
   - Google Drive 正式專案檔案；
   - 可取得的前序專案脈絡。
3. 不要求使用者重新敘述已完成歷史，也不得以缺少完整聊天轉錄為由重做已封版工作。
4. 只有當工作**尚未形成正式 Single-Handoff 且對話必須中止**時，才建立極簡未完成狀態交接。最低欄位固定為：
   `目前節點｜已完成｜未完成｜Blocker｜下一步`
5. 極簡未完成狀態交接不得重抄整套規則、歷史研究或 Excel 結構，也不得取代 GPT→Gemini、GPT→Final Auditor、Final Audit、Excel Executor 或 V0～V8 等正式 Stage Chain 文件。
6. 跨 ChatGPT 對話續作檔本身不是新的工程技術證據；技術事實仍以正式原始證據、當前權威工作包與正式 Excel 為準。

---

# 十、GPT 三層自檢

## 10.1 自檢①｜初判完整性

送 Gemini R1 前確認：
- `Related Source Page Discovery＝完成`
- `Related Source Page Set＝建立`
- 材料名／同義詞／Material-ID／圖號或設備代號之關聯頁搜尋完成
- 已知 CNS／標準號跨頁搜尋完成
- 材料表／設備表／工程材料試驗彙整表／一般說明／施工規範／大樣圖／交互引用頁已納入發現範圍
- 所有命中頁原始頁面影像已重新查看
- 所有 Excluded Related Page 均有排除理由
- `Related-Page Coverage＝PASS`
- 必要條件已完整拆解
- 候選身分清楚
- 主要證據可追溯
- 無明顯跨型號／跨系列拼接

## 10.2 自檢②｜前段收斂完整性

Gemini 最後一輪後確認：
- Gemini 已完成獨立 Related Source Page Discovery
- `Related-Page Coverage＝PASS`
- 實質差異均已裁決
- 未閉合事項已正確分類 U／RFI／F
- 最終候選矩陣一致
- 不需正常 Gemini R3

## 10.3 自檢③｜最終工作包完整性

送 Final Auditor 前確認：
- 最終技術定稿已固定
- Candidate-ID／材料ID／細項ID 固定
- 每一候選逐項 P/U1/U2/U3/F/N/A 已固定
- 判定理由、證據來源、證據型號、證據 URL／文件可追溯
- RFI／缺件與疑似限制競爭狀態固定
- V8 五頁寫入定位清楚
- 安全 Excel 母版檔名／SHA 已記錄；若啟用 `Deferred Excel Batch`，此僅為技術凍結當下之 `Technical Freeze Reference Snapshot`，不得自動延續為後續 Excel 執行母版；批次正式封版時仍須依第二十四節重新解析 `Current Formal Excel Baseline` 並固定其檔名／SHA
- 預定輸出版本明確；Deferred Excel Batch 可改記批次預定輸出版本
- Executor 明確；Deferred Excel Batch 在技術凍結階段可記 `Executor＝Batch Seal 時固定`，但批次正式封版前必須唯一化
- V0～V8 驗證目標已列
- 當前運作模式（Normal／Reserve／Failover／Safe Stop）已明確標註，且 Reserve／Failover 之 MCR 命中判斷已依第二十一節完成

必須明寫：
`GPT自檢③＝通過`

---

# 十一、GPT→Final Auditor 最終工作包

單材料原則只產生一份最終權威工作包：
- Normal／Reserve High Risk：`Mxx_GPT_to_Claude-E_最終工作包_Vn.md`
- Reserve／Failover Low Risk：`Mxx_GPT_to_Gemini-FA_最終工作包_Vn.md`

若 GPT 已完成多材料批次最終整合，得產生一份多材料最終權威工作包，例如：
- `M36-M40_GPT_to_Claude-FA_Final_Audit_Batch_Vn.md`
- `M36-M40_GPT_to_Gemini-FA_Final_Audit_Batch_Vn.md`

多材料最終工作包內，每一材料的必要條件、Candidate-ID、P/U/F、RFI、MCR、自檢與證據鏈仍須獨立可追溯。不得因 Final Auditor 不同而拆成多份互相競爭的技術包。

最低內容：

1. 材料編號／圖號／名稱
2. 適用規範版本
3. 指定原圖頁面與來源識別
4. GPT／Gemini 各輪來源識別／SHA
5. GPT 對 Gemini 最後裁決
6. `GPT自檢②＝通過`
7. 最終必要條件主檔
8. 最終候選逐項判定矩陣
9. Candidate-ID
10. 材料ID
11. 細項ID
12. 最終 P/U1/U2/U3/F/N/A
13. 判定理由
14. 證據來源
15. 證據對應型號
16. 證據 URL／文件
17. RFI／缺件
18. 疑似限制競爭
19. V8 五頁寫入定位
20. `GPT自檢③＝通過`
21. 安全 Excel 母版檔名／SHA
22. 預定輸出版本
23. Executor
24. V0～V8 驗證目標
25. Final Auditor 最後審核要點
26. 當前運作模式與（如適用）風險分類依據（MCR命中項或「全部否」）
27. `Source Grounding Gate＝PASS`
28. `Deferred Excel Batch＝是／否`
29. 若為是：Batch-ID、批次範圍、`Technical Freeze Gate` 狀態、Final Audit 狀態、Excel Eligibility 狀態、目前 Batch Manifest 檔名／SHA

此工作包中的 P/U/F 已是正式技術結果，不是等待 Excel 再判定的中間資料。

---

# 十二、Claude-E 最後審核

Claude-E 不是第三個反覆討論 AI。

主要檢查：
- 原圖有無重大錯讀
- 必要條件有無漏／重
- Candidate-ID 有無混型號
- P/F 最低證據強度是否對稱
- 是否跨系列／跨證書
- RFI 是否錯誤關閉
- V8 五頁是否完整
- 唯一鍵是否正確
- 輕量公式是否符合 V8
- A/B/C/D/E 機械規則是否正確
- 是否存在外部活頁簿連結
- 母版 SHA／預定版本／Executor 是否一致
- V0～V8 是否可執行

Claude-FA 不得把最後審核變成重新建立一套依 Excel 證據欄位判 P/U/F 的引擎。

**Final Audit Scope Lock：** GPT 最終工作包已具 `Source Grounding Gate＝PASS` 後，Claude-FA 的審核範圍鎖定於上述高風險技術項目、實際命中的 MCR、關鍵原圖／P-F 證據／Candidate-ID／證書範圍。除非自然發現會改變技術結論的重大矛盾，不得重做 GPT＋Gemini 全部市場研究、重新廣泛搜尋所有候選、重跑完整 Source Grounding，或為既有 U1／U2 無限擴張研究。Excel 結構／公式／V0～V8 安全性由 GPT-W 執行階段負責。

### 12.0.1 Shared-Page Visual Audit Deduplication｜Final Audit影像去重

Multi-Material Final Audit Batch 中，若兩個以上材料引用**相同來源檔案、相同頁碼**，Final Auditor 應以「唯一頁」為影像載入單位：

1. 同一原圖頁原則只載入／渲染一次；
2. 同一次視覺檢查可同時支援該頁涉及之多個材料，但每材料之必要條件與技術結論仍須獨立判定；
3. 不得僅為滿足「逐材料獨立查核」而對完全相同頁面重複載入；
4. 若不同材料需查看同頁不同區域，可在同一頁影像上分別檢查，不必重新載入整頁；
5. 只有來源 bytes／版本改變、既有影像清晰度不足、或新爭點需要原先未涵蓋區域時，才新增載入或裁切。

### 12.0.2 Canonical Crop Reuse｜標準裁切重用

同一 Final Audit Batch／同一來源版本中，某頁已建立足以判讀的關鍵區域裁切後：
- 後續材料若使用同一區域，直接重用該裁切；
- 不重複做相同倍率、相同範圍之裁切／放大；
- 若需更小字體、不同欄位或不同細節，才追加新的局部裁切；
- 裁切重用只重用「視覺證據取得動作」，不得把某材料的 P/F 結論自動套用到另一材料。

### 12.0.3 Final Audit Visual Index｜GPT送審索引

多材料 Final Audit 工作包應由 GPT 優先提供一份精簡 `Final Audit Visual Index`，至少列：
`來源檔｜唯一頁碼｜涉及材料｜關鍵區域／圖名｜主要審核風險`

同一頁只列一次，再於「涉及材料」欄列出所有 Mxx。此索引只用於去重與定位，不取代 Final Auditor 自行查看原圖。

Combined Run（最多10項）送 Claude-FA 時，`Final Audit Visual Index` 另須以 Segment A／Segment B 標示涉及材料，並優先把共用頁／共用正式證據去重成唯一索引。Claude-FA 工作包不得重複附 Gemini 歷史回覆、GPT 前段研究全文或每材料重貼相同證據；其目的為使 10 項 Combined Final 的 Token／工具負荷以「唯一高風險證據」而非「10份完整研究紀錄」成長。

### 12.0.4 Token Budget ≠ Scope Waiver

Final Audit Token／工具額度不足，不得自行把制度性必查範圍縮成「只查使用者點名項目」。

資源優化順序固定為：
`共用頁去重 → 共用裁切重用 → 共通敘述壓縮 → 已閉合低風險證據不重查 → 保留全部制度性必查高風險項`

若依上述優化後仍無法完成最低 Final Audit，應回資源 Blocker／分段執行，不得以省 Token 為由靜默省略必查項。

### 12.0.5 Audit Count Integrity｜稽核頁數紀錄完整性

Final Audit 摘要若回報原圖查核數量，必須同時列：
- `Unique Source Pages＝N`
- `Material-Page Links＝N`（如有需要）
- `Additional Crops＝N`（如有使用）

`Unique Source Pages` 必須與列舉之唯一頁碼實際數量一致。頁碼重複只計一次；不得出現「寫 7 頁但列出 8 個唯一頁碼」之紀錄矛盾。


## 12.1 PASS

無重大錯誤時，Claude-FA 只固定：

`Final Audit＝PASS`

並結束本輪 Claude 任務。

標準逐材料封版後續流程：
1. GPT 固定可追溯 Final Audit PASS 識別；
2. GPT 重新解析／固定 Current Formal Excel Baseline、預定輸出版本與 `Executor＝GPT-W／ChatGPT Work`；
3. GPT 建立最小化 `Excel Execution Package／Excel Write Payload`；
4. GPT-W 執行第十四節 `Execution Capacity Preflight`；
5. Preflight＝PASS 後才建立 candidate、寫入 V8 正式資料；
6. GPT-W 執行必要 recalc、V0～V7、V8-L、正式 SHA；
7. 可安全直傳 Drive 時由 GPT-W 完成 V8-D；否則交一般 GPT Drive Seal Relay。

若 GPT-W `Execution Capacity Preflight＝FAIL`，Claude Final Audit PASS 保持有效，但不得建立 candidate，回：
`Excel Execution Deferred｜Capacity Preflight Failed`

**Deferred Excel Batch 例外：** 逐材料 Claude-FA Final Audit PASS 後只把 PASS 識別與 SHA 納入 Technical Freeze／Batch Manifest；到 Batch Formal Seal Gate 才由 GPT 建立整批 Executor Package，交 GPT-W 執行。
## 12.2 FAIL

有重大錯誤時：
`Excel Write Blocker｜Final Audit Failed`

只列：
- 問題
- 影響
- 證據
- GPT 需修正內容

不得先寫 Excel，也不得另寫一份完整技術研究報告。

---

# 十三、A／B／C／D／E 機械規則

A／B／C／D／E 由 Excel 依固定規則計算：

1. 若資料結構／唯一鍵錯誤 → `資料錯誤`
2. 若 F > 0 → `E`
3. 若關鍵 RFI「暫停評級＝是」 → `D`
4. 若候選身分未明確，或候選範圍不是完整圖說品項 → `C`
5. 若全部適用必要條件均為 P → `A`
6. 若至少 1 項 P、無 F、無關鍵 D → `B`
7. 其他 → `C`

完成率：
`P ÷ (必要條件總數 - N/A)`

若 `必要條件總數 - N/A = 0`：
- 完成率顯示 `N/A` 或空白，不得產生 `#DIV/0!`；
- 不得把「零個適用條件」視為「全部適用條件均 P」而自動判 A；
- 若無其他既有規則可形成 A／B／C／D／E，維持 `尚未評分`。

因此：
- **P/U/F＝AI 技術判斷**
- **A/B/C/D/E＝Excel 機械計算**

Excel 只機械使用已定稿之 P/U/F，不得以 A/B/C/D/E 反向改寫技術判定。

---

# 十四、Excel 低負載與安全寫入

## 14.1 Execution Capacity Preflight｜寫入前執行能力閘門

任何 Executor 在建立／修改 candidate 前，必須依當下已知工具狀態與權限，先固定 `Execution Strategy＝Single-Pass／Planned-Checkpoint`。Preflight 要確認完整執行鏈可由選定策略安全完成；若依批次規模、結構位移、公式／Table 數量、既往同類執行紀錄或已知模型／工具額度，單一工作階段有高度機率在 V8 前耗盡資源，**不得假裝 Single-Pass 可行**，應在第一次寫入前直接採 `Planned-Checkpoint`：
- 安全母版檔名／SHA 與最終工作包 SHA／預定版本／Executor 核對；
- 在任何資料變更前完成目標寫入區與相鄰區之結構盤點：合併儲存格、Excel Table 範圍、公式引用、資料驗證、名稱範圍、既有區塊邊界、正式母版預留容量與預定 V8 寫入定位；
- 從安全母版建立全新 candidate；
- 固定 `Execution Strategy`；若為 `Planned-Checkpoint`，先固定 Stage-ID、每階段目標工作表／區塊、允許的結構動作、預期 checkpoint 數與最終一次 recalc／V0～V8 時點；
- 標準流程：當前單一材料既定資料寫入；Deferred Excel Batch：批次範圍內全部已達 Excel Eligibility＝Ready 之材料仍屬**同一 batch candidate**，但可依 14.2.3 在多個安全執行階段完成，不要求同一聊天／同一模型工作階段一次塞完；
- 必要 recalc；
- V1～V6；
- V7 關閉後重新開啟；
- V8 正式升版；
- 正式輸出 SHA；
- 簡潔驗證報告；
- 若 Executor 本身無法安全直傳 Drive，至少能把最終 `.xlsx` 原始位元以可下載／可移交的實體檔案方式完整交給 GPT Seal Relay，且能先固定檔案大小與 SHA-256。

**Drive 上傳本身可由 GPT Seal Relay 接手，因此「Executor 缺少 Drive 上傳工具」不單獨構成 Preflight FAIL；但若最終 workbook 原始位元無法完整離開 Executor 環境、無法取得可核對 SHA 的實體檔，則 Preflight 必須 FAIL，不得開始寫入。**

若必要工具、權限、檔案讀寫或執行鏈狀態不足以合理確認可完整做完：
`Execution Capacity Preflight＝FAIL`

處理：
- 不建立 candidate；
- 不開始 Excel 寫入；
- 不以未規劃的「先做一點」方式留下半成品；若已選 `Planned-Checkpoint`，只能依事前 Stage Plan 產生受控 checkpoint；
- 停止於已完成的技術定稿／Final Audit 節點；
- 回 `Excel Execution Deferred｜Capacity Preflight Failed`。

Preflight 是寫入前安全閘門，不代表對平台資源做不可能的絕對保證；但已知不穩定、工具不可用或無法完成必要驗證時不得冒險開始。

## 14.2 低負載與批次執行

Executor 應：
- 每次只處理 1 workbook；
- 標準逐材料封版每次只處理 1 材料；
- `Adjacent Batch Combined Run` 啟用且兩個相鄰 Batch 已完成同一 Combined Final／Final Audit PASS 時，可由一份正式 Execution Package 在同一 candidate 一次處理最多 10 項；仍只處理 1 workbook；
- `Deferred Excel Batch` 正式封版時例外：同一 candidate 可一次處理已固定 Batch Manifest 內的整批材料，但不得混入 Manifest 範圍外材料；
- 以穩定 ID／複合鍵定位；
- 優先小區塊／矩陣批次寫入；
- 避免逐列／逐欄各自建立一次工具呼叫；
- V1～V6 優先以單一驗證腳本／批次檢查完成；
- V7 單獨關閉／重開；
- V8 最後升版與計算 SHA；
- 保留公式、資料驗證與工作簿封裝完整性。

### 14.2.1 Excel 結構安全寫入硬規則｜All Executors（V2.8新增）

本款為既有 `Execution Capacity Preflight` 與安全寫入責任之具體最低要求，不新增新的 Gate。Normal Mode 由 GPT-W／ChatGPT Work 遵守；只有使用者明確啟動 Claude Excel Fallback 時 Claude 才作為合法 Executor 遵守：

1. **先盤點、後變更。** 在第一次寫入、插入、刪除、移動或擴張資料區之前，必須先確認目標區及相鄰區之合併儲存格、Excel Table 範圍、公式引用、資料驗證、名稱範圍、區塊分隔／標籤、既有資料尾端與正式母版預留容量，並形成一次完整寫入計畫。
2. **預留區優先。** 正式安全母版已有足夠預留可寫區域時，必須優先在預留區內追加／填入資料；不得僅為騰出空間而使用插入列／欄或搬動既有資料區塊。
3. **物理位移需事前固定影響圖。** 若確實無法避免插入／刪除／移動列欄，執行前必須先固定預期位移量及其對 merged cells、Table refs、公式、資料驗證、名稱範圍、V8 寫入定位與區塊邊界的完整影響；若所用引擎對任一結構是否會自動位移無法確認，視為結構安全尚未成立，不得用正式 candidate 試錯。
4. **差異比對以穩定鍵為主。** 寫入後資料完整性與前後差異驗證，必須以材料ID、Candidate-ID、細項ID、RFI／疑似ID或其合法複合鍵對應內容；不得僅以「舊 row N 對新 row N」做全表 diff。若存在已規劃之物理位移，必須先套用位移映射再比對，預期位移本身不得誤報為資料變更。
5. **禁止同一 candidate 反覆試寫修補。** 執行中若發現未預期的合併儲存格、Table、公式、資料驗證、名稱範圍或區塊定位衝突，立即停止；若已造成寫入狀態不明或結構被改動，該 candidate 作廢，回到最新正式安全母版重新建立，不得在同一 candidate 反覆插列、回填、修補直到看似通過。
6. **工具無關。** 不得以特定函式庫或軟體名稱取代上述責任；即使工具宣稱會自動位移／擴張，仍須以實際結構驗證結果為準。

禁止：
- 整本無差別重寫；
- 全表無必要排序；
- 大量重套格式；
- 公式轉值；
- 新增未授權工作表／欄位／評分制度；
- 從流程無效舊候選版本續改；
- 建立外部活頁簿依賴；
- 為壓力測試新增永久工作頁；
- 多次無必要重複開關同一檔案。

### 14.2.2 Executor Compact Mode／Excel Write Payload｜Final Audit PASS 後最小化輸入

Final Audit PASS 後，Claude-FA 的 Normal Mode 任務即結束；**不得再把完整技術上下文帶入 Excel 執行階段**。

由 GPT／最終整合者建立 `Excel Write Payload`。標準單材料可直接附於 `Excel Execution Package`；Deferred Excel Batch／Planned-Checkpoint 則由 GPT 產生對應 `Excel Execution Capsule／Continuation Capsule`。上述文件全部都是**機械執行資料包，不是第二份技術工作包**，只能把已固定最終權威 MD／Batch Manifest 轉成精確寫入動作，不得重新研究或改判。

最低內容：

Package 頂部固定加入：
- `Execution Package Status＝READY`
- `Claude-FA＝PASS`
- `User Execution Authorization＝REQUIRED`
- `Package Alone Authorizes Excel Write＝NO`

其後至少包含：
- Current Formal Excel Baseline／或前一 checkpoint 檔名、SHA、大小；
- Final Audit PASS 識別；
- 最終 Batch Manifest／最終工作包檔名與 SHA；
- 預定輸出版本；
- `Executor＝GPT-W／ChatGPT Work`；
- Execution Strategy；
- 當前 Stage-ID 與允許修改之工作表／區塊；
- 穩定鍵及精確目標值／公式／文字；
- 已核准之 insert／delete／move 位移映射（如有）；
- 本 Stage Mini-Validation／最終 Validation；
- 禁止技術重研究／改判。

GPT-W 在執行階段以 Payload 為唯一寫入指令來源；Payload 與 Final Manifest／固定 SHA 不一致時回 `Excel Execution Payload Blocker`，不得自行回頭重做技術研究。

**User Execution Authorization｜既有 Executor 啟動語意：**
- `Excel Execution Package`／`Excel Write Payload` 是正式機械執行規格，不是使用者授權憑證；
- GPT-W／ChatGPT Work 在建立新 candidate 前，必須收到一次明確的使用者執行授權，例如：  
  `正式授權 GPT-W／ChatGPT Work：依 <Execution Package正式檔名>，以 <Current Formal Excel Baseline／Execution Base> 為基準，執行 <範圍>，立即開始 Excel 寫入與 V0～V8。`
- 使用者已明確指定 Execution Base／Execution Package／執行範圍後，不得再要求第二次開始確認；
- User Authorization 只啟動既有機械執行鏈，不得修改、補充或覆蓋任何 GPT Final／Source Grounding／自檢②③／Claude-FA PASS／Execution Package 內的技術結論；
- 授權後且前置條件成立時，固定路由為：  
  `User Authorization → 取得 sealed baseline raw bytes → 建立新 candidate → 寫入 → recalc → V0～V8 → V8-L → Drive raw-byte SHA → Formal Seal`
- 已明確授權後，不得停在 `READY` 等待一般性再次確認。

**已授權後允許停止之 Blocker：**
僅在下列執行前置或安全條件失敗時停止，並固定回：
`Excel Execution Blocker｜<具體原因>`

允許原因：
1. sealed baseline raw bytes 無法取得；
2. baseline SHA 不符；
3. Execution Package 缺失／版本不符；
4. Claude-FA 未 PASS；
5. 使用者授權範圍與工作包衝突；
6. Excel 結構安全 Gate／既有結構安全檢查 FAIL。

不得以一般性的「尚未確認」「請再確認是否開始」阻擋已明確授權之任務。

**Execution Package 結尾固定附授權指令範本：**
`正式授權 GPT-W／ChatGPT Work：依 <Execution Package正式檔名>，以 <Execution Base／Current Formal Excel Baseline> 為基準，執行 <執行範圍>，立即開始 Excel 寫入、recalc、V0～V8、V8-L 與後續 Formal Seal。`

**Fresh Work Execution Session：** Deferred Excel Batch、前一 Work 執行工作階段已接近工具／上下文上限、或 Preflight 判定需要 Planned-Checkpoint 時，應使用全新 ChatGPT Work 執行工作階段。只要唯一 Executor 仍為 GPT-W、沒有其他 AI 寫入 workbook、checkpoint／Payload SHA 鏈一致，換 Work 工作階段不構成 Executor 更換，不使 Claude Final Audit PASS 失效，也不新增技術節點。
### 14.2.3 Planned Safe Checkpoint Segmentation｜計畫式安全分段

`Planned-Checkpoint` 是正式低負載策略，不是把 batch 拆成多個正式版本。成立後：
1. 整批仍只有 **1 個 batch candidate／1 個預定正式輸出版本／1 個唯一 Executor**；
2. 可依工作表或穩定區塊分 Stage，例如「主檔／判定明細」「RFI／疑似／候選摘要」「公式／版本字樣」「recalc＋V0～V8」；不得藉 Stage 改變技術內容；
3. 每一 Stage 只執行 Payload 明列之原子動作；完成後 save＋close，固定不可變 checkpoint；
4. checkpoint 建議正式暫存命名 `<預定輸出檔名 stem>__CP01.xlsx`、`__CP02.xlsx`…，每個 checkpoint 記 `Parent Checkpoint SHA`，形成單向 SHA 鏈；不得覆寫已固定 checkpoint bytes；
5. 每一中間 checkpoint 只做 `Checkpoint Mini-Validation`：可開／關、SHA／大小、受影響區 merged cells／Table／公式／資料驗證／名稱範圍、穩定鍵、位移映射、`Pending Atomic Write＝0`、`Unresolved Structure Conflict＝0`；**中間不跑完整 recalc、不跑 V0～V8、不建立正式 Excel 版本**；
6. 下一個 Stage 可在全新 GPT-W／ChatGPT Work 執行工作階段進行，但寫入前仍須依 21.6.3 對前一 checkpoint 做 Resume Gate；
7. 只有最後 Stage 完成全部寫入後，才一次執行必要 recalc、V0～V7、V8-L；Drive Seal 仍依第25節；
8. 任一 Stage 出現 Unsafe Interruption／結構不明，當前 checkpoint 不成立；依 21.6.1 作廢並從最後一個已通過 Gate 的 checkpoint（若存在）或 Current Formal Excel Baseline 重建合法後續；不得在損壞檔上修補。

本機制的目的是降低單一 Work 執行工作階段的工具／上下文壓力；**不得**用來增加 Gemini 輪次、重新 Final Audit、建立逐材料正式 Excel 或規避最終 V0～V8。

## 14.3 Write-Phase Research Freeze｜寫入階段研究凍結

一旦 candidate 正式開始寫入：
- 原則停止網路研究與新增技術研究；
- 不重新改判 P/U1/U2/U3/F/N/A；
- 不重新找新候選；
- 不重開 GPT／Gemini／Claude 一般技術討論；
- 只允許處理執行中自然發現、且會直接破壞技術結果或 Excel 安全性的重大 Blocker。

若出現足以改變技術結論的新重大正式證據：立即停止、candidate 作廢，回到既有 Targeted Reopen／GPT 修正規則；不得在半成品上邊研究邊改。

## 14.4 PASS 後執行資源優先順序

Final Audit PASS 後，Executor 的執行資源優先順序固定為：
1. Excel 寫入完整性；
2. recalc；
3. V0～V8；
4. 正式輸出 SHA；
5. 簡潔驗證報告；
6. 其他說明文字。

不得先把主要資源消耗在已定稿技術內容重述、非 Blocker 額外研究、排版評論或長篇方法學說明。

測試應由執行環境完成，無須在正式 workbook 常駐額外測試頁。Reserve／Failover Mode 下同樣適用；第二十一節之 D 子表為本規範第三節已授權之既有欄位擴充，不視為未授權新增。

---

## 14.5 Deferred Excel Batch｜批次 Excel 執行邊界

`Deferred Excel Batch` 只改變 Excel 寫入與正式封版時點，不改變逐材料技術判定、Final Audit 路由或四模式。

批次正式寫入時：
- 只允許 1 個 workbook candidate；
- 只允許 1 個唯一授權 Executor；
- 只寫入最終 Batch Manifest 列入且 `Excel Eligibility＝Ready` 的材料；
- 以同一最新正式安全母版建立 candidate；
- 寫入前執行「整批」 Execution Capacity Preflight；
- 不產生逐材料中間正式 Excel 版本；
- `Single-Pass` 時全批寫入後一次 recalc、V0～V8、V7 reopen、V8 正式升版與 SHA；`Planned-Checkpoint` 時可依 14.2.3 分 Stage 寫入，但仍只在最後 Stage 一次 recalc＋V0～V8；
- V8 完成前若屬 `Unsafe Interruption` 或重大 Blocker，整個 batch candidate 作廢；若屬 `Controlled Resource Stop`，只有依 §21.6 固定 Resumable Checkpoint 並於下一輪通過 Resume Gate 才可續作。已固定之逐材料技術 MD／Final Audit PASS 不因純 Excel 停止事件自動失效。

本制度不得新增 Excel 工作頁、欄位、公式、評分或治理引擎。Batch Manifest 一律存在 MD／Stage Chain，不寫入新的 Excel 結構。

---

# 十五、V0～V8（V8版）

- **V0**：來源版本／母版 SHA／Executor／預定輸出版本／`Execution Capacity Preflight＝PASS`／寫入開始時間；標準流程記最終工作包 SHA，Deferred Excel Batch 則另記最終 Batch Manifest 檔名／SHA、批次範圍及 Manifest 內全部逐材料最終工作包 SHA 清單，且寫入開始時間必須晚於最終 Manifest SHA 固定時間
- **V1**：五工作頁存在且無未授權多餘工作頁
- **V2**：材料ID／Candidate-ID／RFI／疑似ID／必要條件鍵／逐項鍵唯一；合併儲存格／Table／資料驗證／名稱範圍與預定寫入結構無未處理衝突
- **V3**：必要條件與候選映射完整
- **V4**：P/U1/U2/U3/F/N/A 原始值合法，A/B/C/D/E 機械結果一致
- **V5**：輕量公式正常、無外部活頁簿連結、無公式錯誤
- **V6**：抽樣測試 A/B/C/D/E 規則與完成率
- **V7**：關閉後第二次重新開啟，確認封裝、公式、資料、Table／合併儲存格／資料驗證／名稱範圍仍正常；若曾有物理位移，依固定映射與穩定鍵確認非目標資料未被誤改
- **V8-L｜Local Seal Candidate**：正式升版、固定最終 `.xlsx` bytes、檔案大小、輸出 SHA-256 與本地驗證內容。若尚未完成 Drive raw-byte 回讀，只能記 `V8 Local Validation＝PASS｜V8 Formal Seal＝PENDING`。
- **V8-D｜Drive Seal Confirmation**：將 V8-L 完全相同 bytes 上傳正式 Drive 位置，再 raw-byte 下載回讀；Drive bytes 大小與 SHA-256 必須與 V8-L 完全一致。只有 V8-L＋V8-D 都 PASS，才可記 `V8 Formal Seal＝PASS`／`V0～V8＝PASS`。

標準逐材料流程 V8 完成前不得開始下一材料；Deferred Excel Batch 則依第十八節，在指定 Range 內可於 `Technical Freeze Gate＝PASS` 後進入下一材料，但到批次終點後仍必須完成 Batch Formal Seal Gate。

---

# 十六、Blocker 後處理

## 16.1 非新技術證據

若問題僅為：
- SHA
- Candidate-ID
- 唯一鍵
- V8 寫入定位
- 公式
- 工作表結構
- 文字遺漏
- Executor／版本不一致

由 GPT 直接修正並重發一份新版最終工作包，不回 Gemini。

## 16.2 真正新的重大技術證據

只有新證據足以推翻材料身分、必要條件、確切型號或 P/F，才可啟動一次 Targeted Reopen。

---

# 十七、疑似限制競爭

「疑似限制競爭」是查核警示，不等同直接判定違法綁標。

應分開記錄：
- 形成疑似的技術條件
- 市場可替代性
- 是否存在多品牌／多型號可同時符合
- 是否需 RFI 或設計監造釐清
- 判定所依證據

不得僅因找到少數候選就直接宣告違法。

---

# 十八、前一材料完成閘門與 Deferred Excel Batch

## 18.1 標準逐材料正式完成閘門｜Default

未啟用 Deferred Excel Batch 時，開始 M(n+1) 前必須確認：
- GPT 最終工作包已固定；
- 依當前 Mode 完成正式 Final Audit PASS（Claude-E 或 Gemini-FA；`Deferred for Claude`／Safe Stop 不得視為 PASS）；
- 正式 Excel 已由授權 Executor 生成；
- V0～V8 全部通過；
- 新 Excel SHA 已記錄；
- 無未解除 Blocker。

只有完成上述閘門，才能開始下一材料。Failover／Reserve Mode 下之等效條件仍依第二十一節，核心要求不放寬。

## 18.2 Deferred Excel Batch｜使用者明確啟用之封版策略

`Deferred Excel Batch` 是 **Excel 封版策略／workflow overlay**，不是第五種 Mode。Normal／Reserve／Failover／Safe Stop 四模式及每材料 Final Auditor 路由完全不變。

只能由使用者明確啟用，最低指令需固定：
`Deferred Excel Batch＝User Enabled｜Batch-ID＝___｜Range＝M起點～M終點`

規則：
- 未明確啟用時一律回到 18.1；
- 不得由任何 AI 自行啟用、擴張批次範圍或延長終點；
- 終點變更、取消或拆批均需使用者明確指示並建立新版 Batch Manifest；
- 本制度只延後 Excel，不降低原圖影像查核、GPT＋Gemini 兩輪封頂、Source Grounding、MCR、P/F 對稱、RFI、限制競爭或 Final Audit 品質；
- 不得因批次制度主動跳過「當前 Mode 下原本可執行」的 Final Audit。Failover High Risk 的 `Deferred for Claude` 仍依既有制度存在。

本節是 18.1「完成 H 才能下一材料」的**唯一明文例外**：在指定批次範圍內，可由 18.3 `Technical Freeze Gate＝PASS` 取代逐材料 Excel 完成閘門，先進入下一材料；但該材料仍不是正式完成。

## 18.3 Technical Freeze Gate｜技術凍結門檻

批次範圍內，開始下一材料前，當前材料至少必須：
1. 完成指定原圖影像複核；
2. 完成 GPT＋Gemini 技術鏈及 GPT 自檢②③；
3. 產生唯一最終權威 MD，檔名／版本／SHA 固定；
4. `Source Grounding Gate＝PASS`；
5. MCR-01～11 已由 GPT／Gemini 固定，當前 Mode／風險路由明確；
6. P/U1/U2/U3/F/N/A、RFI／缺件、疑似限制競爭均已定稿；
7. Final Audit 狀態已依當前 Mode 記錄：
   - `Final Audit＝PASS` → `Excel Eligibility＝Ready`；
   - 僅 Failover High Risk 等既有制度合法形成之 `Deferred for Claude` → 可技術凍結，但 `Excel Eligibility＝Not Ready`；
   - `Final Audit Failed／Blocker` → Technical Freeze 不得 PASS，必須先修正；
8. 最新 Batch Manifest 已納入該材料之固定 SHA 與狀態。

全部成立時記：
`Technical Freeze Gate＝PASS｜Formal Completion＝NO｜Excel Deferred＝YES`

**技術凍結不等於正式完成、不等於 Excel 已封版、不等於 V0～V8 PASS。**

若凍結後出現足以改變材料技術結論之新正式證據，或最終權威 MD 內容改變：
- 只重開受影響材料；
- 原 SHA／Manifest entry 保留歷史但標示 superseded；
- 建立新版材料 MD＋新 SHA；
- Batch Manifest 升版並重算 SHA；
- 其他未受影響材料不得因此重做。

## 18.4 Batch Manifest｜批次控制索引

Batch Manifest 是正式 Stage Chain 控制 MD，不是工程技術證據，也不取代逐材料最終權威 MD。

最低欄位：
- Batch-ID／案件／Range／啟用紀錄；
- Manifest 版本／SHA；
- 材料編號／材料ID；
- 逐材料最終權威 MD 檔名／SHA；
- `Source Grounding Gate`；
- MCR 結果與當前 Mode／Final Auditor 路由；
- Final Audit 狀態與 PASS／Blocker 識別；
- `Technical Freeze Gate`；
- `Excel Eligibility＝Ready／Not Ready`；
- RFI／Blocker 狀態；
- 技術凍結當下參考安全母版檔名／SHA；
- 預定批次輸出版本；
- Executor（未到封版前可記 `Batch Seal 時固定`）。

每次新增材料、材料 SHA 變更、Final Audit 狀態變更、批次範圍或 Executor 變更，均須建立新版 Manifest 並重算 SHA；不得靜默覆寫既有 Manifest 歷史。批次正式封版前必須固定最終 Manifest SHA。

## 18.5 Batch Formal Seal Gate｜批次正式封版門檻

只有以下全部成立，才可開始整批 Excel：
1. Batch Range 每一材料均存在，無缺號或超出範圍材料；
2. 全部材料 `Technical Freeze Gate＝PASS`；
3. 全部材料最終 `Excel Eligibility＝Ready`；`Deferred for Claude`、Final Audit Pending／FAIL／Blocker 均不得進入寫入；
4. 全部逐材料最終權威 MD／SHA 固定，且與最終 Batch Manifest 一致；
5. 最終 Batch Manifest 檔名／SHA 固定；
6. 無未解除重大 Blocker；
7. 批次寫入所用 `Current Formal Excel Baseline` 已依第二十四節完成正式封版證據鏈與 Drive 實檔 SHA 核對並固定；若與某材料技術凍結時參考母版 SHA 不同，必須先執行 Baseline Rebase／Excel 安全／寫入定位相容性複核；若差異只涉及全域 ID、列位或結構映射，依第二十四節機械修正並升版工作包／Manifest，不觸發技術重開；若差異會改變技術內容，才重開受影響材料；
8. 整批唯一 Executor 已由使用者／正式路由固定，同一批次不得競寫；
9. `Batch Execution Capacity Preflight＝PASS`，並固定 `Execution Strategy＝Single-Pass／Planned-Checkpoint`；合理確認可由選定策略完成整批寫入、最後一次 recalc、V0～V8、V7 reopen、SHA 與驗證報告。若單一工作階段已知高機率耗盡資源，應選 Planned-Checkpoint。

通過後：
- Executor 從最新正式安全母版建立 **一個全新 batch candidate**；
- 依最終 Manifest 寫入整批材料；Single-Pass 可一次完成，Planned-Checkpoint 可依 14.2.3 在同一 candidate 的多個 Stage 完成；
- 不建立逐材料中間正式 Excel 版本；planned checkpoint 亦僅為暫存 SHA 鏈，不是正式版本；
- 全批所有寫入完成後才一次 recalc；
- 執行 V0～V8、V7 reopen、正式 SHA 與批次驗證報告；
- 只有 `V8 Formal Seal＝PASS`（含 `Drive raw-byte SHA＝PASS`）後，Manifest 內材料才由 `Formal Completion＝NO` 一次轉為 `Formal Completion＝YES`。

若 batch candidate 在 V8 完成前停止或失敗：
- `Unsafe Interruption`／Blocker：candidate 全部作廢，不允許接續半成品；
- `Controlled Resource Stop`：僅可依 §21.6 建立 Resumable Checkpoint，下一輪通過 Resume Gate 後續作；
- 已固定 Technical Freeze／逐材料 MD 不因純 Excel 中斷自動失效；
- 已正式形成且符合 PASS Persistence 條件之 Final Audit PASS 保持有效；
- 只有 `Unsafe Interruption`、Checkpoint Resume FAIL 或無合法 checkpoint 可用時，下一次才必須從最新 Current Formal Excel Baseline 建立全新 batch candidate；合法 Planned／Controlled checkpoint 通過 Resume Gate 時可由該 checkpoint 續作。

到達指定批次終點後，在 Batch Formal Seal Gate 完成前不得開始 M(終點+1)。


### 18.5.1 Adjacent Batch Combined Run 與既有 Batch Formal Completion

當兩個相鄰既有 Batch 以 §8.1.4 Combined Run 執行：
- Combined Run 不取代兩個原 Batch 的正式身份；
- Excel 只建立 1 個 candidate、1 個正式輸出版本、1 個 Current Formal Excel Baseline；
- V0～V8／V8-L／Drive raw-byte SHA／Formal Seal 只執行一次；
- Formal Completion 可由 1 份 Combined Completion 報告封裝，但必須逐 Batch 明列：
  - `<Batch A> Formal Completion＝YES`
  - `<Batch B> Formal Completion＝YES`
- Master Register／歷史查詢仍以原 Batch-ID 追溯；
- 不建立 `B06X`、`B065`、`G06` 或其他新 Batch-ID。

若 Combined Run 在 Final Audit 或 Excel Preflight 前觸發 `Combined Run Safety Split`，後續回到兩個原 Batch 各自依既有流程完成；不得產生競爭 candidate 或把部分 Combined candidate 當正式母版。

## 18.6 V8 材料狀態模型

---

# 十九、正式狀態

標準逐材料流程：
A｜GPT 初判＋自檢①  
B｜Gemini Round 1  
C｜GPT 裁決  
D｜Gemini Round 2（如需要）  
E｜GPT 最終定稿＋自檢②③＋單一最終工作包  
F｜Final Audit（Claude-FA 或依 Mode 之 Gemini-FA）  
G｜Excel＋V0～V7 完成  
H｜V8 Formal Seal＝PASS＋Drive raw-byte SHA＝PASS＋Formal Completion＝YES

標準流程只有 H 可以開始下一材料。

Deferred Excel Batch 啟用時另允許：
T｜`Technical Freeze Gate＝PASS｜Formal Completion＝NO｜Excel Deferred＝YES`

在使用者指定 Batch Range 內，T 可開始下一材料；但 T **不是 H、不是正式完成、不可宣稱 Excel 已封版**。到達批次終點後，必須完成 18.5 Batch Formal Seal Gate，Manifest 內全部材料才正式轉為 H。

---

# 二十、本版禁止回退

自 V2.0 起：
- 不得把 Excel 當成第二套技術判定引擎；
- 不得由 Excel 依證據欄位重新判 P/U/F；
- 不得要求正式 workbook 常駐額外壓力測試頁；
- 不得把已濃縮進 `查核明細與規則` 的規則拆回多個固定工作頁；
- 不得在 Normal Mode 讓 Claude 同時兼任 Final Auditor 與 Excel Executor；只有使用者明確啟動 `Claude Excel Fallback＝Enabled` 的特定版本例外；
- 不得恢復正常 Gemini 第三輪或無限討論制度；
- 不得自行增加第六工作頁、欄位或新評分制度；
- 不得把 Deferred Excel Batch 視為第五種 Mode、不得自行啟用或把它變成未經使用者明確授權的預設流程；
- 不得把 `Technical Freeze Gate＝PASS` 冒充正式完成或 V8 PASS。

V8 的精簡與 Deferred Excel Batch 都只改變 Excel 承擔／封版時點，不降低原圖、證據、必要條件、確切型號、P/F 對稱、RFI 與限制競爭查核品質。

自 V2.1 起，另不得：
- 把 Reserve／Failover Mode 當成規避 Claude-E 第三方品質控制的常態化途徑；
- 讓 GPT 或 Gemini 自行宣告 Claude 不可用；
- 讓「低風險」判定回到 GPT 主觀信心，而非 MCR 客觀清單比對；
- 將 Claude QC Sampling 抽查率降為 0；
- 因本次修改新增工作表、風險分數、AI信心分數或跨頁公式鏈。

---

# 二十一、Claude Availability、Reserve Mode 與 Failover（V2.2封版）

## 21.1 制度目的

本節目的不是等 Claude 額度耗盡後才找替代方案，而是把 Claude 視為稀缺的第三模型品質控制資源，將其運算集中在真正需要獨立高階審核的位置。

硬規則：
- GPT＋Gemini 前段技術品質不得因 Normal／Reserve／Failover／Safe Stop 任一模式而降低；
- Claude 不必對每個已高度收斂的低風險材料重做完整研究；
- Claude 的第三模型品質控制權不得消失；
- AI 流程治理不得轉嫁成新的 Excel 公式引擎。

## 21.2 四種運作模式

四模式定義維持不變。`Deferred Excel Batch` 僅為第十八節之 Excel 封版策略 overlay，不是第五種 Mode；啟用後每一材料仍各自依本節判定 Normal／Reserve／Failover／Safe Stop 與 Final Auditor 路由。 各模式路由文字中原本指向「Excel／GPT-W Excel」的終點，在 Deferred Excel Batch 啟用時僅表示該材料於 Final Audit PASS 後取得 `Excel Eligibility＝Ready`，實際 Excel 寫入統一延至 18.5 Batch Formal Seal Gate。

### 21.2.1 Normal Mode

Claude 額度正常：

`GPT → Gemini → GPT最終定稿 → Claude-FA Final Audit → GPT-W／ChatGPT Work → V0～V8`

沿用第八節標準流程。Claude-FA 聚焦高風險 Final Audit，不重做 GPT＋Gemini 已完成之完整市場研究；PASS 後不再執行 Excel。

### 21.2.2 Reserve Mode

僅由**使用者明確啟動**方可生效，目的為保存 Claude 額度。

每一材料均須先完成 21.4 `Claude Mandatory Review Checklist`：
- 任一 MCR＝是 → `High Risk` → `Claude-E Final Audit`
- 全部 MCR＝否 → `Low Risk` → 原則走 `Gemini-FA Cold Audit`
- GPT 與 Gemini 對任一 MCR 判斷不同 → 一律 `High Risk`

Reserve Mode 下 Low Risk 材料仍納入 21.5 Claude QC Sampling，不得永久脫離 Claude 監督。

Reserve Mode 的 Excel Executor 同樣固定為 `GPT-W／ChatGPT Work`。Claude 僅負責依 MCR／QC 規則執行 Final Audit／品質控制；除非使用者另行明確啟動 `Claude Excel Fallback＝Enabled`，否則 Claude 不寫 Excel。

### 21.2.3 Failover Mode

僅由使用者明確確認 Claude 目前不可使用方可啟動，必須記錄：

`Failover Trigger＝User Confirmed`

GPT、Gemini、Claude 均不得自行代替使用者宣告 Failover。

Failover Mode 仍必須完成 MCR：
- 全部 MCR＝否 → `Gemini-FA Cold Audit → GPT-W Excel`
- 任一 MCR＝是 → `Deferred for Claude`，不得強迫封版

因此 Failover 不設主觀「極高風險」第二層；是否可在 Claude 不可用時封版，以 MCR 客觀結果為準。

使用者啟動 Failover Mode，即同時視為授權 `GPT-W` 為該 Failover 預定輸出版本之唯一 Excel Executor。

### 21.2.4 Safe Stop Mode

Claude 與 Gemini 均不可用：

**停在 GPT 最終技術定稿，不生成正式封版 Excel。**

不得讓 GPT 同時兼任技術主責、Final Auditor 與正式 Executor。

恢復路徑：
- Claude 先恢復 → 對尚未封版之 GPT 最終工作包執行 Claude-FA Final Audit，PASS 後交 GPT-W 完成 Excel；
- 僅 Gemini 先恢復而 Claude 仍不可用 → 需由使用者明確啟動 Failover，且僅 MCR 全否之案件可走 Gemini-FA；
- Safe Stop 期間未封版之案件不屬 Failover 回溯抽樣池，因其尚無正式 Gemini-FA 封版成果可供回溯。

## 21.3 Gemini-FA＝Gemini Final Auditor（Cold Audit）

Gemini-FA 必須：
- 使用全新 Gemini 對話；
- 不讀 Gemini 自己先前 R1／R2 回覆；
- 不視為 Gemini R3；
- 不與 GPT 再展開正常技術討論；
- 只讀一份 GPT 最終權威工作包、正式共通規範／Gemini Instructions、指定原圖及必要高風險正式證據；
- 重新從原圖與正式證據找錯，不以自己前段結論作為正確性前提。

只允許兩種正式結果：

`Final Audit PASS｜Fallback Auditor = Gemini-FA`

或

`Excel Write Blocker｜Fallback Final Audit Failed`

若 FAIL，只列會改變技術結論或封版安全性的 Blocker，不另開新一輪 GPT↔Gemini 正常討論。

## 21.4 Claude Mandatory Review Checklist（MCR-01～11）

「低風險」不得以 GPT 主觀信心成立。GPT 與 Gemini 均須在前段技術鏈內獨立核對下列項目：

1. `MCR-01` P↔F 可能翻轉
2. `MCR-02` 必要條件數量／拆解／定義仍有實質爭議
3. `MCR-03` 跨型號／跨系列／跨證書疑義
4. `MCR-04` 原圖存在兩種以上合理解讀
5. `MCR-05` U3 或高強度證據衝突
6. `MCR-06` 疑似限制競爭核心結論可能改變
7. `MCR-07` GPT 與 Gemini 最後技術輪仍有實質分歧
8. `MCR-08` 新材料類型／新判定模式／尚未有成熟案例
9. `MCR-09` 前一同類材料曾發生重大誤判、仍在加強監督期
10. `MCR-10` RFI 是否應關閉存在疑義
11. `MCR-11` 候選身分／候選範圍是否為完整圖說品項存在疑義

判定規則：
- 任一項＝是 → `High Risk`
- 全部＝否 → `Low Risk`
- GPT／Gemini 對任一項不同 → `High Risk`
- Normal／Reserve 下 High Risk → `Claude Mandatory`
- Failover 下 High Risk → `Deferred for Claude`
- Safe Stop 下不執行封版

MCR 僅用於 AI 工作包與靜態留痕，不得建立 Excel 風險分數、加權分數或 High／Low Risk 公式引擎。

## 21.5 Claude QC Sampling（抽樣稽核）

不得將所有 Gemini-FA 案件恢復後全部交 Claude 重審；抽樣目的在維持第三模型監督，同時節省 Claude Token。

### 21.5.1 Calibration Phase

Gemini-FA 機制首次正式啟用時，以最先完成之 3 件 Gemini-FA 正式封版案例為校準池：
- Claude 可用或恢復後，至少對其中 2 件做**輕量回溯 QC**；
- 此為一次性的機制校準例外，允許與 Gemini-FA 對同一材料形成有限重疊；
- QC 僅查：必要條件漏／重、跨型號／跨系列／跨證書、P/F 證據強度；若涉及 RFI 關閉或候選完整性，再加查該項；
- 不重做完整市場研究、不全量重讀 URL、不重算 Excel。

Calibration 通過後進入 Steady State。若後續制度發生重大修訂或出現制度性錯誤，可由使用者明確指示重新進入 Calibration。

### 21.5.2 Steady State 最低下限

最低 Claude 品質監督率固定為：

**每 10 件 Gemini-FA／Low Risk 正式路徑至少 1 件接受 Claude 品質控制，永遠不得降為 0。**

抽樣計數由 AI Stage Chain／工作包管理，Excel 不自動排程、不建立抽樣公式。

### 21.5.3 Reserve Mode 前置控制抽樣

Calibration 完成後，Reserve Mode 優先採前置控制抽樣：
- Low Risk pool 每累積 10 件，至少 1 件在 Gemini-FA 前直接改由 Claude-E Final Audit；
- 該件不再由 Gemini-FA 重複審核；
- 其餘 Low Risk 可走 Gemini-FA；
- 目的為避免穩態下「Gemini-FA一次＋Claude再一次」的重複 Token 消耗。

### 21.5.4 Failover Mode 回溯抽樣

Failover 當下 Claude 不可用，因此先由 Gemini-FA 完成符合條件之 Low Risk 案件。

Claude 恢復後：
- 從已正式封版之 Failover pool 做最低 1/10 輕量回溯 QC；
- QC 範圍同 21.5.1；
- 不重做完整 Final Audit。

### 21.5.5 品質回饋迴路

Claude QC 發現重大錯誤：
- 第一次重大錯誤 → 後續監督率至少提高至 1/5；
- 短期再次重大錯誤 → 至少提高至 1/3；
- 制度性錯誤 → 該類材料暫停 Gemini-FA 路徑，於 Normal／Reserve 重新列入 Claude Mandatory；Failover 時則 `Deferred for Claude`，直到規則修正完成。

若回溯 QC 發現已封版材料存在重大錯誤：
- 不直接覆寫原正式檔；
- 建立 `QC Blocker`／更正工作包，重新走必要技術修正與新版本封版；
- 原版本須被明確標示為待更正／已被後續版本取代，不得靜默修改歷史 SHA。

## 21.5.6 Deferred Excel Batch 與 QC 計數

Deferred Excel Batch 不改變 Calibration／Steady State／1/10／1/5／1/3 比例，但「Technical Freeze」不得冒充正式封版：
- `Technical Freeze Gate＝PASS` 尚未進入 Gemini-FA「正式封版案例」計數；
- Calibration 所稱「最先 3 件正式封版案例」以 Batch Formal Seal Gate 完成、V8 PASS 後才成立；
- Failover 回溯 QC pool 亦以批次正式封版後才納入；
- Reserve Mode 前置控制抽樣是技術路由控制，不得因 Excel 延後而延後：Low Risk pool 累積與 1/10 前置改派 Claude-E 仍照原規則在 Gemini-FA 前執行；
- 若同一批次一次使多件材料正式封版，計數按 Manifest 中逐材料條目逐件計算，不把整批視為 1 件。

本款只澄清計數時點，不改抽樣比例與 Claude Token 邊界。

## 21.6 Executor 中斷、Planned／Controlled Checkpoint Resume 與 Final Audit PASS Persistence

本節先區分兩種完全不同的停止狀態。**不得把任何未封版半成品自動視為可續作 checkpoint。**

### 21.6.1 Unsafe Interruption｜不安全中斷

符合任一項即屬 `Unsafe Interruption`：
- 工具錯誤、逾時、斷線或額度中止發生於檔案寫入／儲存動作進行中，無法證明該動作完整完成；
- workbook 是否成功儲存／關閉不明；
- 無法取得完整可重新開啟之實體檔；
- 發生未閉合的 merged cells、Table、公式、資料驗證、名稱範圍、區塊定位或其他結構衝突；
- 寫入狀態、已完成範圍或待寫範圍無法可靠重建；
- Executor 已更換；
- GPT 最終工作包、Batch Manifest、Current Formal Excel Baseline、預定技術內容或高風險正式證據已實質改變；
- checkpoint 必要欄位不完整且無法依 21.6.4 完成一次性 Legacy Recovery。

處理：
- 發生錯誤之**當前未固定 Stage 輸出／暫存工作檔**一律作廢，不得從狀態不明檔續作；
- 若 `Execution Strategy＝Planned-Checkpoint` 且已有上一個 immutable checkpoint 完整 SHA／Record，該 parent checkpoint 不因後續 Stage 失敗而失效；下一次仍須先對 parent checkpoint 通過 Resume Gate 才可重做失敗 Stage；
- 若沒有合法 parent checkpoint，才必須從當下合法 `Current Formal Excel Baseline` 建立全新 candidate；
- 同一預定輸出版本同一時間只允許一個正式 Executor。

### 21.6.2 Controlled Resource Stop｜可控資源停止

只有在**非技術、非結構、非寫入錯誤**的資源因素（例如工具額度／單回合執行額度／工作階段即將結束）迫使停止，而且停止前已完成當前原子寫入步驟時，才可建立 `Resumable Checkpoint`。

成立條件全部必須為真：
- Executor 未更換；
- 當前寫入／insert／delete／move／save 動作均已完整結束；
- workbook 已明確成功儲存並可正常關閉；
- 可取得完整實體檔；
- `Write State＝Known`；
- `Pending Atomic Write＝0`；
- `Unresolved Structure Conflict＝0`；
- 無新重大正式技術證據或新 Blocker；
- GPT 最終工作包、Batch Manifest 與 Execution Base 未改變。

停止前必須建立一份可追溯 `Checkpoint Record`，至少固定：
1. Checkpoint 檔名；
2. Checkpoint SHA-256；
3. Checkpoint 檔案大小；
4. `Current Formal Excel Baseline` 檔名／SHA；
5. GPT 最終工作包或最終 Batch Manifest 檔名／SHA；
6. Executor；
7. 預定輸出版本；
8. 已完成步驟；
9. 尚未完成步驟；
10. 已修改工作表／區塊；
11. 曾執行之 insert／delete／move 與位移映射；
12. 已處理之 merged cells／Table／公式／資料驗證／名稱範圍修正；
13. recalc 狀態；
14. V0～V8 目前狀態；
15. `Write State＝Known｜Pending Atomic Write＝0｜Unresolved Structure Conflict＝0`；
16. `Checkpoint Formal Completion＝NO`。

`Resumable Checkpoint` 不是正式 Excel、不是 V8-L、不是 V8 Formal Seal、不是 `Current Formal Excel Baseline`，也不得被任何後續流程引用為正式母版。

`Controlled Resource Stop` 仍是非預期資源停止的安全例外；但自 V2.13 起，若 Preflight 已判定單一工作階段高機率耗盡資源，可依 14.2.3 **事前選擇 `Planned Safe Checkpoint Segmentation`**。兩者都必須維持同一 candidate／同一 Executor／同一預定輸出版本與 checkpoint SHA 鏈；禁止臨時無計畫反覆寫入。

### 21.6.2A Planned Safe Checkpoint｜事前計畫型節點

若 `Execution Strategy＝Planned-Checkpoint`，不必等到額度即將耗盡才停止。Executor 應在每個事前固定 Stage 邊界主動 save＋close，完成 `Checkpoint Mini-Validation` 後固定 checkpoint SHA／大小／Parent SHA／完成與未完成 Stage。此為**受控執行節點**，不是錯誤、不是 Executor 中斷、不是 Formal Completion。

為降低上下文負擔，Stage 之間可切換到全新 GPT-W／ChatGPT Work 執行工作階段；只要唯一 Executor 仍為 GPT-W、沒有第二 AI 寫入、且前一 checkpoint 與 `Excel Execution Capsule` SHA 可追溯，視為同一 Executor 持續執行。若使用者明確啟動 Claude Excel Fallback，則同理可切換全新 Claude fallback 執行對話。

### 21.6.3 Checkpoint Resume Gate｜續作前強制驗證

下一輪／下一工作階段不得直接續寫。先在**不修改 workbook**的情況下完成：
- 重新計算 Checkpoint SHA／大小，必須與前次固定值一致；
- GPT 最終工作包／Batch Manifest SHA 未變；
- Current Formal Excel Baseline 識別與 SHA 未變；
- Executor 未變；
- 預定技術內容未變，當前 `Excel Execution Capsule／Excel Write Payload` SHA 與 Stage-ID 可追溯；
- workbook 可正常開啟；
- 對所有已修改區執行結構定向複核：merged cells、Table refs、公式引用、資料驗證、名稱範圍、區塊邊界；
- 以材料ID、Candidate-ID、細項ID、RFI／疑似ID及合法複合鍵確認已完成內容與 Checkpoint Record 一致；
- 若曾有物理位移，依固定影響圖／位移映射確認非目標資料無誤改；
- 無新重大正式技術證據、無新 Blocker。

全部 PASS 才可記：

`Checkpoint Resume Gate＝PASS`

並從 **Checkpoint Record 所列第一個未完成步驟** 繼續，不要求重做已驗證完成之機械寫入。

任一項 FAIL：

`Checkpoint Resume Gate＝FAIL｜Checkpoint Discarded`

並依 21.6.1 從合法 Current Formal Excel Baseline 重建。

### 21.6.4 Legacy Checkpoint Recovery Gate｜舊規則暫存檔一次性恢復

若暫存檔是在 V2.12 生效前，依舊規則產生，因而沒有完整 Checkpoint SHA／Record，但其停止原因明確為純資源耗盡，允許**一次性**做 Legacy Recovery；此例外不得擴張成日後常態。

恢復時必須先停止一切 Excel 寫入，只做唯讀驗證；至少確認：
- 同一 Executor；
- 原 GPT 最終工作包／Batch Manifest／Execution Base 均未改；
- 對現有暫存檔計算並固定 SHA／大小；
- workbook 可正常開啟／關閉；
- 逐一核對已回報完成之工作表、穩定鍵、Table refs、公式、merged cells、資料驗證、名稱範圍與位移映射；
- 對原正式母版做穩定鍵／合法映射差異確認，證明變更僅限已授權範圍；
- 建立完整「已完成／未完成」清單與本節 21.6.2 的 Checkpoint Record。

全部 PASS 才可記：

`Legacy Checkpoint Recovery＝PASS｜Recovered Resumable Checkpoint`

之後仍須再通過 21.6.3 `Checkpoint Resume Gate` 才能續寫。

任一項無法證明：

`Legacy Checkpoint Recovery＝FAIL｜Checkpoint Discarded`

不得以「看起來正確」或文字進度摘要取代實檔驗證。

### 21.6.5 Final Audit PASS Persistence

若停止前已有**可追溯的正式 `Final Audit＝PASS` 紀錄**，且以下全部未變：
- GPT 最終工作包及其 SHA；
- 正式安全母版及其 SHA；
- 預定技術內容；
- 無新重大正式技術證據；
- 無新 Blocker；

則純 Excel／recalc／V0～V8 的 Unsafe Interruption、Controlled Resource Stop 或 Executor 續作事件本身**不使該 Final Audit PASS 自動失效**，不得因此完整重跑 Final Audit 或重新啟動 Gemini。若 Executor 更換，既有 PASS 是否保留仍依本節條件判斷，但工作檔不得跨 Executor 續作。

Deferred Excel Batch 同樣適用：
- Unsafe Interruption → batch candidate 作廢；
- Controlled Resource Stop → 只有符合 21.6.2 且通過 21.6.3 才可續作同一 checkpoint；
- 未改變之 Technical Freeze MD／Manifest entry 與符合條件的 Final Audit PASS 可保留；
- 不得因純 Excel 停止事件重開 Gemini 或全量 Final Audit。

若任一技術基準改變，PASS Persistence 不適用。若只有批次正式封版時採用之安全母版 SHA 與技術凍結時參考母版不同，先依第十八節做 Excel 安全／寫入定位相容性複核；只有差異會影響技術內容、Final Audit 高風險判斷或 V8 寫入定位時，才重開受影響部分。

本節與第 1.4、十二、十四、十五節之安全寫入與驗證原則一致。

## 21.7 Failover／Reserve 留痕

不寫入 `RFI與缺件追蹤`。

僅在既有 `查核明細與規則` D 子表保存最低限度靜態紀錄：

`材料ID｜Mode｜Final Auditor｜Executor｜觸發原因｜使用者確認｜風險分類依據｜Claude抽查狀態｜抽查結果`

「風險分類依據」填：
- `Mandatory Checklist＝全部否`；或
- 具體 `MCR-xx`。

所有欄位均為寫入值；不得建立風險分數或抽樣公式。

## 21.8 Excel 輕量化硬限制

Availability／Reserve／Gemini-FA／Claude QC Sampling 的流程邏輯存在於 MD、Stage Chain 與 AI 工作包中。

V8 Excel 只保存最低限度追溯結果。不得因本制度：
- 新增工作表；
- 建立 Claude 風險分數；
- 建立 AI 信心分數；
- 建立新的 High／Low Risk 公式引擎；
- 建立跨頁 Failover 公式鏈；
- 讓 Excel 自動管理 1/10、1/5、1/3 抽樣排程；
- 新增常駐測試頁；
- 把 AI 流程治理重新塞回 Excel。

V8 原有必要的唯一鍵、完成率、A/B/C/D/E 與必要一致性檢查維持原責任。

若新制度必須依靠大量新增欄位、公式或工作表才能成立，視為設計錯誤，不採用。

## 21.9 觸發權與模式變更硬規則

- Reserve／Failover 均只能由使用者明確啟動；
- GPT／Gemini／Claude 可指出「建議進入某模式」的風險事實，但不得自行切換模式；
- `Failover Trigger＝User Confirmed` 為 Failover 必要記錄；
- 模式恢復或解除亦以使用者明確指示為準；
- AI 不需要求帳號截圖、額度頁面或其他證明。


---

# 二十二、來源可追溯與零腦補（Source Grounding / Anti-Fabrication Gate）（V2.3新增）

## 22.1 核心原則

所有 AI 在本專案執行工程圖說、規範、標單、估價、材料、設備、品牌、型號、CNS、單價分析與 Excel 寫入時，均須遵守：

> **任何數據、單價、規格、數量、尺寸、材質、性能、品牌、製造商、型號、CNS／標準、試驗值、工率、損耗率或技術敘述，只要要被當成「事實」使用，就必須能說明它從哪裡來。**

不得因為某數字「合理、常見、像市場行情、符合工程經驗」就自行補入並當成已確認資料。

本規則適用於所有模式（Normal／Reserve／Failover／Safe Stop）及所有 AI 角色，不因進度、Token、執行環境或 Excel Executor 改變而放寬。

## 22.2 五種合法來源狀態

每一個會影響判斷或金額的資料，至少應屬下列之一：

1. **S1｜原始文件直接值**：正式圖說、契約、規範、標單、型錄、證書、試驗報告、正式 Excel 或其他原始文件中明確出現。
2. **S2｜使用者／公司正式資料**：使用者明確提供之公司成本、工班報價、內部資料庫、正式既有決議。
3. **S3｜透明計算衍生值**：原值未直接出現在文件中，但可由已確認輸入值依明確公式計算；必須能指出輸入值來源與計算式。
4. **S4｜外部查證值**：經公開原廠、政府、CNS／認證、正式供應商或其他可追溯外部來源查得；必須標示來源，不得偽裝成原圖既有資料。
5. **S5｜暫定假設值**：只有在任務本身允許估算／暫定時才能使用，且必須明確標示「暫定猜測」或同義警示，不得當成已確認事實。

未能落入 S1～S5 任一類者，一律視為：

`UNSUPPORTED｜疑似AI憑空捏造`

## 22.3 「前文沒有出現」不等於一定錯誤

AI 不得機械地把所有「表格／圖說沒有直接出現」的數字都判成捏造。

例如：
- 由圖說尺寸計算出的面積，可屬 S3；
- 使用者提供之工班成本，可屬 S2；
- 經正式網路查價取得之市場單價，可屬 S4。

真正的判定問題是：**能否回溯到來源或透明計算鏈。**

若無法回溯，即使數字看起來合理，也不得保留為正式值。

## 22.4 標單／估價專用嚴格覆核

處理標單、估價單、單價分析、成本資料庫時，**GPT 主責、Gemini 獨立複核**，必須以「最嚴苛標單覆核員」標準逐項檢查；Claude-E 不因本款新增逐項重查責任：

- 工程數量
- 單位
- 材料單價
- 工資
- 機具費
- 搬運／耗損
- 配比
- 工率／產能
- 品牌／型號
- CNS／規格
- 稅、運費或其他加成
- 小計／合計與其計算關係

對每一項至少問一次：

> 「這個值在原始表格、圖說、使用者正式資料、透明計算或外部證據中究竟從哪裡來？」

若答案只有「依經驗」「大約如此」「一般市場應該是」「模型推測」，且未明確標成 S5 暫定假設，一律列入警告。

## 22.5 警告清單與更正義務

發現 `UNSUPPORTED` 時，不得只提醒而保留原值。必須：

1. 列入「疑似憑空捏造警告清單」；
2. 指出原位置／欄位／材料／工項；
3. 說明缺少什麼來源；
4. 依優先順序更正：
   - 有正確來源 → 換成來源支持值；
   - 可透明計算 → 改成 S3 並列計算依據；
   - 任務允許暫估但尚無來源 → 明確改標 `暫定猜測`；
   - 不允許暫估或會影響正式判定 → 刪除／留白／改 U1、U2、RFI、缺件或 Blocker（依任務性質）。

不得用另一個沒有來源的新數字去「更正」原本沒有來源的數字。

## 22.6 GPT執行責任

GPT 是 Source Grounding Gate 的**第一責任者**。`Related-Page Source Coverage Gate` 為其原圖來源覆蓋子閘門；在 Necessary Requirements 固定前必須先完成頁面發現與原圖影像確認。

在：
- 送 Gemini R1 前；
- Gemini 回覆後的裁決／收斂；
- 最終技術定稿前；
- 產出標單／估價／單價分析前；
- 產出 Final Auditor 工作包前；

均須主動掃描「看似合理但來源不存在」的內容。此檢查整合進既有自檢①～③，不新增處理節點。

GPT＋Gemini 的詳細警告清單、來源追溯與更正紀錄留在前段工作包／審查紀錄中，不重複塞入 Final Auditor 工作包。

送 Final Auditor 前，GPT 只需在既有自檢③／Stage Chain 中加入一個極短狀態：

`Source Grounding Gate＝PASS`

代表前段 `UNSUPPORTED` 已完成處理；**不得把尚未完成的來源追溯工作推給 Claude-E。**

## 22.7 Gemini執行責任

Gemini 是 Source Grounding Gate 的**獨立第二防線**。Gemini R1 同時是 Related-Page Source Coverage 的獨立第二防線：不得把 GPT 頁碼清單當完整集合，必須先完成獨立頁面發現與 `Related-Page Coverage＝PASS`。

Gemini R1／R2 及 Gemini-FA 必須主動挑出：
- GPT 無法指出來源的數據；
- 規格／CNS／型號由語意猜測補成；
- 把系列級資料當確切型號資料；
- 把市場常識或經驗值偽裝成圖說要求；
- 計算結果缺少來源輸入值或計算式；
- S5 暫定值在後續被誤當正式值。

Gemini 不得因 GPT 已提供完整表格，就假定每個欄位都有證據；也不得以「Claude 後面會再查」作為放行理由。

## 22.8 Claude-E執行責任｜Token-neutral

本節**不得增加 Claude-E 的常態 Token 消耗**。

Claude-E 不負責：
- 重新逐項追查所有數據／單價／規格來源；
- 再做一次完整 Anti-Fabrication 掃描；
- 為 S1～S5 建立第二套來源矩陣；
- 重做 GPT＋Gemini 已完成之標單逐項覆核；
- 因本節而擴張既有 Final Audit／QC 的抽查範圍。

`Source Grounding Gate＝PASS` 是 GPT 在**送出 Final Auditor 工作包之前**必須完成的 Stage Chain 前置條件，不新增為 Claude-E 的 Final Audit 檢查項，也不得要求 Claude 為此讀取前段警告清單或來源矩陣。

Claude-E 只依原有 Final Audit／QC 範圍工作。若 Claude-E 在**原本就要檢查的高風險項目**中自然發現：
- 高影響值明顯無來源；
- 工作包宣告 PASS 但仍存在未處理 `UNSUPPORTED`；
- 來源與實際原圖／證據直接矛盾；

則可回：

`Final Audit Blocker｜Unsupported / Suspected Fabrication`

此為既有 Final Audit 發現異常後的處理方式，**不是要求 Claude-E 另增加一輪來源查核**。

## 22.9 Claude Token零增量硬限制

防腦補制度的主要 Token 成本由 GPT＋Gemini 承擔。除 Claude-E 原本就必須執行的 Final Audit／QC 外：
- 不得新增 Claude 專用來源查核回合；
- 不得要求 Claude 對標單／估價逐項重查；
- 不得因 `Source Grounding Gate` 增加 Claude QC 抽樣比例；
- 不得因本節新增 Claude 必審 MCR 項目；
- 不得要求 Claude 重讀 GPT／Gemini 歷史來源鏈。

只有 GPT／Gemini 已完成 `Related-Page Coverage＝PASS` 與 Source Grounding Gate 後，工作包才可送入 Final Auditor。

Related-Page Coverage 的加入不得：
- 新增 Claude 專用查核回合；
- 新增 Claude MCR；
- 提高 Claude QC 抽樣比例；
- 要求 Claude 重做全文搜尋或完整頁面發現；
- 新增 Excel 工作表／欄位／分數／治理公式。

## 22.10 Excel輕量化硬限制

本節屬 AI 思考與稽核規則，**不得因此讓 V8 Excel 變臃腫**。

因此：
- 不新增工作表；
- 不建立「來源分數」「幻覺分數」「可信度分數」；
- 不建立逐欄自動查來源公式；
- 不建立跨頁反腦補公式鏈；
- 不增加常駐測試頁；
- 原則使用既有「來源／證據／備註／單價分析」等欄位承載必要資訊；
- 警告清單優先存在 AI 回覆或 Single-Handoff MD，不強迫 Excel 新增欄位。

若某項來源追蹤必須改 Excel 結構才能完成，須另行向使用者提出必要性並取得明確同意。

## 22.11 禁止事項

禁止 AI：
- 補一個原圖不存在的尺寸；
- 補一個使用者沒提供、網路也沒查證的單價；
- 補一個未經來源支持的品牌或型號；
- 補一個似乎「應該適用」但文件未寫的 CNS／標準；
- 自創工率、損耗率、配比、係數或加成並當作正式數據；
- 為讓總價湊到合理範圍而反推不存在的單價；
- 將 S5 暫定假設在後續版本中悄悄升格為已確認值。

**查不到就是查不到；證據不足就保留不確定性，不得以 AI 腦補填滿空白。**

---

# 二十三、正式 MD 自動同步 Google Drive 強制規則（V2.9新增）

## 23.1 適用範圍

凡由 ChatGPT／GPT 在本專案中**新建、更新、修正、升版、Re-Seal 或重新封裝**的正式 Markdown 文件，均納入本節，包括但不限於：
- 三 AI 共通規範；
- ChatGPT／Gemini／Claude 專案 Instructions；
- GPT→Gemini、Gemini→GPT、GPT→Final Auditor、Excel Executor 等正式工作包；
- Final Audit／V0～V8／MD 同步／SHA 驗證報告；
- Batch Manifest；
- 極簡交接檔；
- 其他被指定為正式留存或後續 Stage Chain 會引用之 MD。

## 23.2 GPT 自動上傳硬規則

只要本次任務有產生或修改正式 MD，GPT 必須在**同一任務內自行完成 Google Drive 同步**，不得只提供下載連結後等待使用者手動上傳，也不得把「請使用者自行放入 Drive」當作正常流程。

固定順序：
`生成／修正 MD → 計算本機 SHA-256 → 上傳至 Drive 正式資料夾 → 重新讀取 Drive 實檔 → 核對檔名／父資料夾／檔案大小 → 重新計算 Drive 實檔 SHA-256 → 本機 SHA＝Drive SHA → 才可宣告正式完成`

## 23.3 正式資料夾與檔名

- 規範／Instructions／規則驗證報告：上傳至專案 `規範MD` 正式資料夾。
- 材料最終工作包／Batch Manifest／Executor 工作包：上傳至專案既定 `最終工作包` 正式資料夾。
- 若案件已有更細分且經使用者確認之正式資料夾，沿用該資料夾，不另建第二套結構。
- 正式檔名不得帶平台重複尾碼 `(1)`、`(2)`、`(3)`；若平台產生技術性尾碼，正式引用仍以無尾碼工程檔名為準。
- 上述規則同樣適用 `GPT-W Excel Execution Package`：例如本機／下載檔 `..._V1(1).md` 的正式身份仍為 `..._V1.md`；`(1)` 只屬平台重複檔名，不得成為 Package 版本、SHA 鏈或授權指令的一部分。

## 23.4 失敗處理

若 Drive 權限、連線、上傳、移動、重新讀取或 SHA 核對失敗：
- 不得宣告該 MD 已正式同步／封版；
- 必須保留本機生成檔及 SHA；
- 明確回報 `MD Drive Sync Blocker` 與失敗步驟；
- 可在同一任務內自行重試可逆的技術性動作；
- 不得要求使用者重做已完成的 MD 內容。

## 23.5 不增加其他 AI 負擔

本節的 Drive 同步責任由 ChatGPT／GPT 承擔，不要求 Gemini 或 Claude 為此新增技術審查回合、重讀歷史 MD、提高 QC 抽樣率或增加 Token 消耗。Gemini／Claude 若只負責回覆內容，其正式回覆由 GPT 整合或取得後依既有 Stage Chain 留存；不得因檔案同步治理改動 P/U/F 或 Final Audit 結論。

---

# 二十四、Current Formal Excel Baseline／母版動態追蹤與封版不可變規則（V2.10新增）

本節用來解決「每次 Excel 封版後版本已前進，但後續 MD 仍沿用舊母版／舊全域 ID／舊結構」造成的資料鏈錯位。**本節優先於本文件中未另定義之「安全母版」「最新安全母版」「最新已驗證 Excel」等舊文字。**

## 24.1 Current Formal Excel Baseline 正式定義

`Current Formal Excel Baseline` 不是「檔名版本看起來最大」的 Excel，而是當下能同時證明以下全部條件之**最高正式版本**：
1. 正式 Excel 實檔存在；
2. 有對應 V0～V8 PASS／正式封版驗證報告；
3. 驗證報告已固定正式輸出檔名與 SHA-256；
4. Drive 正式檔已同步並可重新讀取；
5. Drive 目前實檔 bytes 重算 SHA 與封版 SHA 完全一致；
6. 無未解除 Formal Baseline／Excel Seal Blocker。

**僅有較高版本檔名、candidate、未完成 V8 的輸出、或沒有對應正式 SHA 驗證報告之檔案，不得自動視為 Current Formal Excel Baseline。**

若存在較高版本檔案但其正式性／SHA 鏈未閉合，不得默默回退舊版繼續寫入，也不得把較高檔名當正式母版；應先形成 `Formal Baseline Integrity Blocker` 並修復版本鏈。

## 24.2 正式 Excel 封版不可變（Immutable Seal）

一旦某 Excel 版本完成 V8 Formal Seal：
- 該「正式版本號＋正式檔名＋SHA-256」即為不可變封印；
- 不得在同一正式版本檔名下替換、重存、修公式、改資料、調結構後仍稱同一封版；
- 任何 bytes 改變均須建立**新 Excel 版本**並重新 V0～V8、SHA、Drive 回讀封版。

若同一正式檔名目前 Drive SHA 與既有封版 SHA 不同：
`Formal Baseline Integrity Blocker｜Same Version SHA Drift`

該同名實檔不得作正式母版。必須擇一：
1. 恢復原封版 bytes，使 SHA 回到既有正式值；或
2. 將現有變更視為新版本來源，依合法流程另行升版並重新封版。

不得以「內容看起來差不多」或「較新的修改應該更好」取代 SHA 封印。

## 24.3 Technical Freeze Reference Snapshot 與 Execution Base 分離

逐材料／批次 MD 中曾記錄的 Excel 檔名／SHA，若是在技術查核當下取得，只代表：
`Technical Freeze Reference Snapshot`

用途：保留當時查核環境與可追溯歷史。它**不是永久 Execution Base**。

真正 Excel 寫入時唯一合法來源為：
`Execution Base＝Current Formal Excel Baseline（於寫入前重新解析）`

因此歷史 MD 不因新 Excel 封版而靜默改寫；但任何新的正式工作包／Manifest／Executor 指令若要引用「目前 Excel 狀態」，必須重新解析當下 Current Formal Excel Baseline，不得把舊 MD 的母版名稱複製成「目前母版」。

## 24.4 新材料／新批次之 Baseline Resolve Gate

GPT 在下列節點若需要使用 Excel 既有狀態，必須先執行 `Baseline Resolve Gate`：
- 新材料 R1 工作包建立前，若要讀既有候選／RFI／疑似／完成狀態；
- GPT 自檢③與最終工作包固定前；
- 新 Batch Manifest 建立或升版前；
- Batch Formal Seal Gate 前；
- Executor／candidate 建立前。

最低核對：
- Current Formal Excel Baseline 檔名／版本／Drive File-ID／SHA；
- 目前全域 Candidate-ID、RFI-ID、疑似ID 是否已被占用；
- 材料既有列與完成狀態；
- 五工作頁／Table／公式／預留區／寫入定位是否仍與工作包假設一致；
- 是否存在比該母版更高但尚未封版或 SHA 異常之版本鏈衝突。

原始圖說與工程技術判定仍以原圖／正式證據為準；Baseline Resolve Gate 只防止 Excel 現況、全域 ID、資料結構與版本鏈使用過時資訊。

## 24.5 前後批次平行時的 Baseline Rebase

若使用者明確允許「前一批 Excel 尚未封版，後一批先做技術查核」，可平行完成：
- 原圖影像複核；
- 必要條件拆解；
- 候選產品／型號研究；
- GPT＋Gemini R1／必要 R2 技術收斂。

但在前一批尚未 Formal Seal 時，後一批不得把舊 Excel 的全域 Candidate-ID／RFI-ID／疑似ID、列位或表格容量視為永久固定。最遲在 `GPT自檢③／Final Auditor 最終工作包正式固定` 前，必須先完成 `Baseline Rebase Gate`；既有舊流程已完成 Final Audit 者，則最遲在 Batch Formal Seal 前補做 Rebase。

Rebase 只比較「舊 Technical Freeze Reference Snapshot → Current Formal Excel Baseline」之新增／占用／結構差異：
- 若候選語意身分、必要條件、P/U/F、RFI 實質內容均不變，只需機械重編全域 ID／列位／結構映射，並升版受影響最終工作包與 Batch Manifest、重算 SHA；**不開 Gemini R3、不重做市場研究、不要求 Claude-E 完整重跑 Final Audit**。
- 若 Rebase 發現會改變候選身分、必要條件、確切型號、P↔F、RFI 實質結論或其他高風險技術事實，才只重開受影響材料。

## 24.6 全域 ID 唯一性

Candidate-ID、RFI-ID、疑似ID 等 Excel 全域識別不得由歷史母版推算後永久沿用。正式固定前必須以 Current Formal Excel Baseline 再確認唯一性。

若發現同一 ID 已在較新正式母版由另一材料占用：
- 不覆寫既有正式 ID；
- 後形成之材料改配下一合法唯一 ID；
- 舊技術判定以「候選語意身分」保持不變；
- 受影響工作包／Manifest 升版並重算 SHA。

純 ID 衝突屬執行／Stage Chain metadata 修正，不構成新重大技術證據。

## 24.7 預定輸出版本動態決定

Excel 預定輸出版本必須在 Formal Seal／Executor 固定時，由 Current Formal Excel Baseline 推導下一個合法版本：
- 不得由數日前舊 MD 永久保留舊「預定輸出版本」；
- 若該版本檔名已存在、已被其他流程占用或已有不同 SHA，不得覆寫；
- 必須改用下一合法版本並更新最終 Manifest／Executor 工作包。

每一個成功 V8 Formal Seal 的新 Excel，立即成為後續 `Current Formal Excel Baseline` 候選；但仍須滿足 24.1 的 Drive 實檔 SHA 一致性，才能被下一個任務使用。

---

# 二十五、Excel Drive Seal Relay／跨平台封印接力（V2.11新增）

## 25.1 目的與角色

當唯一合法 Excel Executor 已完成最終 workbook 寫入、recalc、V0～V7 與 V8-L，但其平台無安全可靠的 Google Drive 原始檔上傳路徑時，可啟用：

`Excel Drive Seal Relay＝GPT`

此時：
- 原 Excel Executor 身分**不變**；
- GPT 只負責接收固定 bytes、比對 SHA、上傳 Drive、raw-byte 回讀、建立必要 SHA sidecar／最終驗證報告／Batch Manifest 封印；
- GPT **不開啟、不重存、不改公式、不改資料、不 recalc、不升版本**，因此不構成第二 Excel Executor；
- 若 GPT 必須修改 workbook 任一 byte，Relay 立即失效，視為 Executor 變更；原 candidate 作廢，依既有規則從合法 Execution Base 重建。

## 25.2 Executor 交付最低欄位

Executor 無法自行完成 Drive Seal 時，至少必須交付：
- 正式預定檔名；
- 可下載／可移交之完整 `.xlsx` 實體檔；
- 檔案大小；
- SHA-256；
- `Execution Capacity Preflight` 結果；
- V0～V7 結果；
- `V8 Local Validation＝PASS`；
- `Drive Seal＝PENDING`；
- `Formal Completion＝NO`。

不得把「本地 V0～V8 PASS」當成正式封版完成；Drive 尚未回讀時，正式表述統一為：

`V8 Local Validation＝PASS｜V8 Formal Seal＝PENDING｜Formal Completion＝NO`

## 25.3 傳輸方式與 base64 邊界

- 優先使用平台原生檔案物件、binary upload、file reference、downloadable artifact 或其他不需把整檔字面值嵌入提示／工具參數的方式。
- 不得因平台只提供大型 base64 內嵌路徑，就冒險將高體積 workbook 強塞進單次工具呼叫。
- **不得在規範中設定固定 KB／MB 門檻**；各平台限制會變動，應依當下工具能力與完整性風險判斷。
- 若 Executor 可產出完整本機檔但無法安全直傳 Drive，改走 GPT Seal Relay，不視為 Excel 寫入失敗。

## 25.4 GPT Seal Relay 固定順序

GPT 收到 Executor 固定檔後：
1. 不修改 workbook，先對收到之實體檔計算 `Relay Input SHA-256` 與檔案大小；
2. 與 Executor 回報之 SHA／大小比對；不一致即停止：`Excel Seal Relay Blocker｜Handoff Bytes Mismatch`；
3. 一致後，將**同一 bytes** 上傳至正式 Excel 資料夾；
4. 取得 Drive File-ID；
5. 以 raw-file 方式重新下載回讀；
6. 重算 Drive raw-byte 大小與 SHA-256；
7. 與 Executor SHA 完全一致後，記：`Drive raw-byte SHA＝PASS`；
8. 需要時建立 SHA sidecar；
9. 建立／升版最終 V0～V8 驗證報告與 Batch Manifest；
10. 只有此時才可記：`V8 Formal Seal＝PASS｜Formal Completion＝YES`；
11. 再依 §24.1 將該版本解析為新的 `Current Formal Excel Baseline`。

## 25.5 使用者人工操作邊界

- 「請使用者自行上傳 Drive」不是正常首選流程；若 GPT 可取得 Executor 原始檔並具 Drive 上傳能力，必須由 GPT 完成 Relay。
- 跨平台之間若沒有直接檔案通道，使用者將 Executor 產出的**原始檔交給 GPT**只屬 transport handoff，不等於把 Drive 封印責任轉嫁給使用者。
- 只有 Executor 與 GPT 均確實無法完成 Drive 上傳時，才可請使用者做最小必要的人工 Drive transport；之後仍須由 GPT／可用工具完成 raw-byte 回讀與 SHA 驗證，否則 `Formal Completion＝NO`。

## 25.6 非技術事件

Excel Drive Seal Relay：
- 不新增 Gemini R1／R2／R3；
- 不觸發 Targeted Reopen；
- 不重跑 Claude-E Final Audit；
- 不重做市場研究或 P/U/F；
- 不新增 Excel 工作表、欄位、評分、公式或治理引擎；
- 不因純傳輸事件改變 Candidate-ID、RFI、疑似限制競爭或任何技術結論。

