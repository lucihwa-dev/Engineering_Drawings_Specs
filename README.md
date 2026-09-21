# 工程圖說設備建材查核

> 台灣營造工程之工程圖說、設備、建材、物料、規格、品牌／型號、CNS、證據與 Excel 查核治理專案。

本專案用於建立一套可追溯、可複核、可封版的工程圖說與建材設備查核流程。核心目標：

- 以原始工程圖說與正式文件為最高技術依據。
- 防止 AI 自行補足圖說未出現的規格、尺寸、CNS、品牌、型號或性能。
- 透過 GPT、Gemini、Claude 三 AI 分工，完成技術分析、交叉複核、最終高風險審核與正式成果封版。
- 將每一項技術判定維持可追溯的證據鏈。
- 將技術判定與 Excel 機械計算／封版責任明確分離。
- 降低重複查核、重複載入圖頁、重複市場研究與不必要 Token 消耗。

---

## 1. 專案定位

適用範圍包含但不限於：

- 建築、結構、水電、電氣、弱電、給排水、消防、空調
- 裝修、防水、門窗、玻璃、五金、燈具與機電設備
- CNS／試驗標準查核
- 廠牌／製造商／型號查核
- RFI／缺件追蹤
- 疑似限制競爭查核
- 工程估價／單價分析之來源追溯

---

## 2. 三 AI 與 Executor 分工

### GPT
`前段技術主責＋最終技術整合者`

負責：
- 每材料／設備重新查看指定原始圖說頁面影像
- 必要條件拆解
- 候選品牌／製造商／確切型號
- `P / U1 / U2 / U3 / F / N/A`
- RFI／缺件／疑似限制競爭
- 來源可追溯與零腦補檢查
- 與 Gemini 最多兩輪收斂
- 最終工作包與 Executor Package
- 必要時執行 Drive Seal Relay

### Gemini
`前段獨立交叉複核者`

負責：
- R1 獨立重看原圖並全面挑錯
- R2 僅處理未閉合差異
- 獨立核對必要條件、候選身分、P/U/F、RFI、MCR、限制競爭與疑似 AI 腦補
- 支援 Multi-Material Batch Review
- 正常流程不得自行新增 R3

### Claude
`Final Auditor only`

負責：
- 最後一次高風險獨立技術審核
- 聚焦原圖重大錯讀、必要條件漏／重、Candidate-ID／型號、P/F 證據對稱、跨型號／系列／證書、RFI、MCR 與重大 `UNSUPPORTED`
- PASS 後退出正常技術鏈
- Normal Mode 不負責正式 Excel 寫入、recalc、V0～V8 或 Drive Seal

### GPT-W / ChatGPT Work
`Default Excel Executor`

負責：
- 依固定技術結果執行正式 Excel 寫入
- 不重新研究或改判 P/U/F
- 執行必要 recalc、V0～V8、V8-L
- 視執行環境完成 Drive Seal，或交 GPT 做 Seal Relay

---

## 3. 標準流程

```text
GPT 初判＋自檢①
        ↓
Gemini R1
        ↓
GPT 裁決
        ↓
Gemini R2（如需要）
        ↓
GPT 最終定稿＋自檢②③
        ↓
Claude-FA Final Audit
        ↓
PASS
        ↓
GPT-W / ChatGPT Work
        ↓
Excel + V0～V8 + SHA + Drive Seal
```

Gemini R1 已完全收斂時，可省略 R2。正常流程不得為追求文字完全一致而新增 Gemini R3。

---

## 4. Multi-Material Batch Review

本專案支援多材料／多設備批次查核。

例如 M36～M40 可由 GPT 建立：

```text
M36-M40_GPT_to_Gemini_R1_Batch_V1.md
```

Gemini 在同一 R1 內逐材料獨立完成複核，每材料標示：

```text
Closed
R2 Required
Material Blocker
```

若只有 M38、M40 尚未閉合，R2 只處理：

```text
M38-M40_GPT_to_Gemini_R2_Batch_V1.md
```

已 `Closed` 的材料不得因其他材料仍有差異而重跑。

---

## 5. 原圖查核硬規則

每一材料／設備都必須重新查看相關原始圖說頁面影像。

不得以以下內容取代原圖：

- OCR
- 舊摘要
- 歷史 MD
- AI 記憶
- 搜尋摘要

圖說未明確出現之內容不得自行補成要求，包括：

- 圖號
- 代碼
- 尺寸
- CNS
- 試驗方法
- 性能值
- 材質
- 品牌
- 型號

---

## 6. 證據與判定制度

| 狀態 | 定義 |
|---|---|
| `P` | 已取得足夠強度之證據，且符合必要條件 |
| `F` | 已取得與 P 相同最低證據強度之反向證據，且明確不符合 |
| `U1` | 尚未取得可用證據 |
| `U2` | 有資料，但證據強度不足、型號未閉合或不足以判 P/F |
| `U3` | 存在同級或更高強度證據衝突 |
| `N/A` | 該條件確實不適用，且有可追溯理由 |

### P / F 對稱原則

```text
查不到 ≠ F
未公開 ≠ F
證據不足 ≠ F
```

只有取得達到與 P 相同最低證據強度之明確反向證據，才可判 `F`。

---

## 7. 零腦補規則

所有會影響技術判定或金額的資料都必須可追溯，包括：

- 數量、單價
- 規格、尺寸、材質
- 品牌、製造商、型號
- CNS、性能、試驗結果

若找不到來源，必須標示：

```text
UNSUPPORTED｜疑似AI憑空捏造
```

不得用另一個沒有來源的數字去修正原本沒有來源的數字。

---

## 8. Candidate-ID 與禁止拼證

每一 Candidate-ID 必須對應：

- 單一確切型號；或
- 明確標示的系列／家族級候選

禁止：

- 跨型號拼接
- 跨系列拼接
- 跨證書拼接
- 用系列 A 的性能補系列 B
- 用品牌級證書直接套到確切型號
- 把不同型號證據拼成一個「看似完整」候選

---

## 9. Final Audit Token / Tool 最佳化

### Shared-Page Visual Audit Deduplication

同一 Batch 中相同：

```text
來源檔 + 頁碼
```

原則只載入一次。

例如 p.80 同時涉及 M37／M38／M40：

```text
p.80 載入一次
→ M37 獨立判定
→ M38 獨立判定
→ M40 獨立判定
```

### Canonical Crop Reuse

同一頁、同一區域已完成足夠清晰裁切後，本 Batch 內可直接重用。

影像可以重用，但不同材料的技術結論不得互相套用。

### Audit Count Integrity

若 Final Audit 回報圖頁數量，至少區分：

```text
Unique Source Pages
Material-Page Links
Additional Crops
```

唯一頁數必須與實際列舉之頁碼一致。

---

## 10. Excel 責任邊界

正式 Excel 只負責保存及機械計算，不重新推導技術判定。

技術鏈負責：

```text
P / U1 / U2 / U3 / F / N/A
```

Excel 負責：

- 保存正式技術結果
- 完成率
- A/B/C/D/E 機械計算
- 唯一鍵
- 漏項／重複檢查
- 少量一致性驗證

正式工作簿邏輯固定為五頁：

1. `材料資格基準`
2. `供應商候選清單`
3. `RFI與缺件追蹤`
4. `疑似名單`
5. `查核明細與規則`

不得未經授權新增：

- 工作表
- 欄位
- AI 信心分數
- 風險評分
- 新公式治理引擎

---

## 11. V0～V8

正式 Excel 封版必須完成既定 V0～V8 驗證，包含：

- 工作包／Manifest 與母版 SHA 固定
- 五工作頁完整
- 材料ID／Candidate-ID／RFI／疑似ID 唯一
- 必要條件映射完整
- P/U/F 原始值合法
- A/B/C/D/E 機械結果正確
- 無公式錯誤
- 無外部活頁簿依賴
- 關閉後重新開啟驗證
- 最終版本與 SHA 固定
- Drive raw-byte SHA 驗證

任何正式封版 Excel bytes 若改變，都必須升新版本並重新驗證。

---

## 12. 建議 Repository 結構

```text
工程圖說設備建材查核/
├── README.md
├── 00_治理規範/
│   ├── 三AI共通協作規範/
│   ├── ChatGPT/
│   ├── Gemini/
│   ├── Claude/
│   └── 同步修正驗證報告/
├── 01_案件/
│   └── <案件名稱>/
│       ├── 00_案件說明/
│       ├── 01_原始圖說索引/
│       ├── 02_GPT工作包/
│       ├── 03_Gemini回覆/
│       ├── 04_Final_Audit/
│       ├── 05_Batch_Manifest/
│       ├── 06_Excel_Execution/
│       └── 07_驗證報告/
├── 02_模板/
│   ├── Single-Handoff/
│   ├── Batch Review/
│   ├── Final Audit/
│   ├── Excel Execution/
│   └── 驗證報告/
├── 03_工具與腳本/
└── 99_Archive/
```

> 原始工程圖、契約、廠商文件、標單、公司成本資料與其他機密文件，是否納入 Git Repository 應依專案權限與保密需求決定。

---

## 13. Git / GitHub 管理建議

### 建議納入 Git

- 正式治理 MD
- Instructions
- README
- Template
- Batch Manifest
- Single-Handoff MD
- 技術驗證報告
- 不含機密資料的腳本
- 版本與 SHA 紀錄

### 建議不要直接公開提交

- 原始工程圖 PDF
- 業主契約
- 公司成本資料
- 未公開報價
- 供應商私人文件
- 含個人資料之文件
- 正式 Excel（若含商業機密）
- API Key、Token、密碼

建議使用 Private Repository、`.gitignore` 或另外的受控檔案儲存空間管理敏感資料。

---

## 14. 檔名與版本規則

正式檔名應：

- 使用明確版本號，例如 `V2.16`
- 不使用下載副本尾碼，例如 `(1)`、`(2)`、`(3)`
- 不靜默覆寫已封版檔案
- 正式內容變更必須升版
- 需要封印之文件應保存 SHA-256

示例：

```text
工程圖說設備建材查核_Gemini專案Instructions_V2.23.md
工程圖說設備建材查核_Claude專案Instructions_V2.23.md
工程圖說設備建材查核_MD同步修正驗證報告_V13.md
```

---

## 15. Current Formal Governance Baseline

目前正式治理基準：

```text
三AI共通協作規範：V2.16
ChatGPT Instructions：V3.24
Gemini Instructions：V2.23
Claude Instructions：V2.23
MD同步修正驗證報告：V13
```

後續若出現更高且已正式驗證／封版版本，應自動以最高正式版本為準，不得回退使用舊版治理規則。

---

## 16. 專案使用原則

開始任何正式案件前：

1. 確認最新正式治理版本。
2. 確認正式原始圖說來源。
3. 確認 Current Formal Excel Baseline。
4. 每一新材料重新查看相關原圖。
5. 完成 GPT → Gemini → GPT 技術收斂。
6. 完成 Final Audit。
7. 由合法 Executor 完成 Excel 與 V0～V8。
8. 完成 SHA 與 Drive Seal。
9. 未完成 Formal Seal 前，不得宣稱正式完成。

---

## 17. 專案狀態

本專案持續維護中。

治理規則、AI 分工、Evidence Gate、Batch Review、Final Audit、Excel Executor 與封版流程，均以 Repository 內最新正式版本文件為準。

---

## License / Usage

本 Repository 若包含工程案件、公司成本、圖說、供應商文件或其他非公開資料，使用權限應依資料所有權、契約義務及專案內部規範管理。

未經授權，不應將機密工程資料或受限制文件公開發布。
