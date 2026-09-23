# 工程圖說設備建材查核
# Related-Page Source Coverage Gate｜同步修正驗證報告 V1

**日期：2026-09-23**  
**性質：Related-Page Source Coverage Gate 治理升版同步驗證**  
**前版正式治理：共通 V2.16｜ChatGPT V3.24｜Gemini V2.23｜Claude V2.23｜V13 FORMAL SEALED**

## 一、觸發缺口

既有治理要求 GPT／Gemini「重看指定原圖」，但未先要求獨立證明指定頁集合已涵蓋全部關聯原圖。

正式修正：
`Related-Page Source Coverage Gate｜關聯原圖頁覆蓋閘門`

工作包既有頁碼只能作為起始集合，不再視為完整封閉集合。

## 二、升版範圍

| 文件 | 舊版 | 新版 | Local SHA-256 |
|---|---|---|---|
| 三AI共通協作規範 | V2.16 | V2.17 | `74437f804271fa3dc5dc67b798d59a760f2ec25e35f6dde61caa0887c3043894` |
| ChatGPT Instructions | V3.24 | V3.25 | `bfb10c8a2e479c62aaa9dcf85774ff809da40fc18021c45f90fa7e41a9b7c0e8` |
| Gemini Instructions | V2.23 | V2.24 | `c49b3f68ed2b20a090b8b2e37de418b942656337eec6853ca2d34ca2b3265897` |
| Claude Instructions | V2.23 | **維持 V2.23，不升版** | N/A |

## 三、正式制度

### GPT 第一責任
- Necessary Requirements 固定前先做 Related Source Page Discovery。
- 建立 `Related Source Page Set`。
- OCR／PDF文字層／全文搜尋只定位。
- 命中頁全部重看原始頁面影像。
- Include／Exclude 均留痕；Exclude 必須有理由。
- 送 Gemini R1 前 `Related-Page Coverage＝PASS`。

### Gemini 獨立第二防線
- GPT Source Page List 只作起始集合。
- R1 自行再做一次 Related Source Page Discovery。
- 新命中頁看原始影像並判 Include／Exclude。
- `Related-Page Coverage≠PASS` 不得 `R1 Status＝Closed`。
- 不新增 R3。

### Claude 邊界
- Claude V2.23 維持不變。
- 不新增 Claude MCR。
- 不提高 QC 抽樣率。
- 不要求 Claude 重跑完整 Related Source Page Discovery。
- 不增加 Claude Final Audit Token。

## 四、V-MD-04 Test Cases

### Case A｜M06
起始頁 A9-1 時，新制度必須允許並要求跨頁發現 A0-3；A0-3 generic「防水材」只能依原圖實際語意納入共通／關聯條件，不得自行改寫成逐字點名 M06。若 A0-3 同列出現 CNS8082／CNS10639，必須由原圖影像確認後納入相應必要條件或來源鏈。

**Expected＝PASS**

### Case B｜M09
起始頁 A9-1，若 Related Source Page Discovery 命中 A9-9／A9-10，而原圖註記「非屬本工程」，必須記：
`Excluded Related Page`
並附排除理由，不得納入 M09 Necessary Requirements。

**Expected＝PASS**

### Case C｜Shared Page
M06～M10 共用 A9-1 時：
- Related Source Page Discovery 可共享 locator／頁面載入結果；
- A9-1 原圖可只載入一次；
- 各材料獨立判定；
- 不得因新增 Coverage Gate 重複載入五次。

**Expected＝PASS**

## 五、驗證結果

| 驗證 | 結果 |
|---|---|
| V-MD-01｜Version Chain | PASS |
| V-MD-02｜Cross-Doc Consistency | PASS |
| V-MD-03｜Workflow Integrity | PASS |
| V-MD-04｜3 Test Cases | PASS |
| V-MD-05｜Anti-Regression | PASS |

Anti-Regression 已確認保留：
- P/F 證據對稱；
- 查不到／未公開／證據不足 ≠ F；
- 禁止跨型號／跨系列／跨證書；
- Source Grounding Gate；
- Shared-Page Visual Audit Deduplication；
- Canonical Crop Reuse；
- Final Audit Scope Lock；
- Claude Token 零增量；
- GPT-W default Excel Executor；
- V0～V8；
- Current Formal Excel Baseline；
- MD Drive Sync。

## 六、Drive Formal Seal

三份升版治理本體已上傳至正式專案 `規範MD` 資料夾，並完成 Drive raw-byte 回讀。

| 文件 | Drive File-ID | file size | Local SHA-256 | Drive SHA-256 | 結果 |
|---|---|---:|---|---|---|
| 三AI共通協作規範 V2.17 | `1LabyhvC9kemjYutEXZw5Rlt8nNatmDYm` | 95915 | `74437f804271fa3dc5dc67b798d59a760f2ec25e35f6dde61caa0887c3043894` | `74437f804271fa3dc5dc67b798d59a760f2ec25e35f6dde61caa0887c3043894` | PASS |
| ChatGPT Instructions V3.25 | `18jgDTk4CLsaWKQJLO0tJVRNWrxDy5HKc` | 44433 | `bfb10c8a2e479c62aaa9dcf85774ff809da40fc18021c45f90fa7e41a9b7c0e8` | `bfb10c8a2e479c62aaa9dcf85774ff809da40fc18021c45f90fa7e41a9b7c0e8` | PASS |
| Gemini Instructions V2.24 | `1huTJJwYL9FbLZDHVgpcAZ9DUolUdRMJg` | 26119 | `c49b3f68ed2b20a090b8b2e37de418b942656337eec6853ca2d34ca2b3265897` | `c49b3f68ed2b20a090b8b2e37de418b942656337eec6853ca2d34ca2b3265897` | PASS |

`Drive raw-byte SHA＝PASS`

## 七、正式結論

`Related-Page Source Coverage Gate＝FORMAL SEALED`

新最高正式治理版本：

`共通 V2.17｜ChatGPT V3.25｜Gemini V2.24｜Claude V2.23`

Claude 維持 V2.23，不重新生成，不增加 Final Audit／QC Token。

本次治理升版只新增 GPT／Gemini 的關聯原圖頁發現與覆蓋責任；不重開既有已封版材料，不修改 P/U/F、MCR、Excel 五工作頁、GPT-W Executor、V0～V8、Shared-Page Visual Audit Deduplication 或 Canonical Crop Reuse。
