# 工程圖說設備建材查核－MD同步修正驗證報告

**版本：V13**  
**日期：2026-09-17**  
**性質：Final Audit Shared-Page／Canonical Crop 去重與 Token Budget 邊界同步驗證**  
**前版：V12**

## 一、觸發問題

Claude 回報 M36～M40 Final Audit 因掃描圖說、多頁影像與重複裁切消耗較多 Token，並提出兩類改善：
1. 同頁共用時合併驗證、避免重複載入；
2. 進一步考慮只驗證使用者指定高風險項目。

前者採納；後者不採納為預設治理規則。

理由：Claude-FA 的價值在獨立高風險 Final Audit，Token Budget 可去除重複工具呼叫，但不得取消制度性必查項。

另發現執行紀錄示例「共查7頁（p.45、46、80、81、82、96、97、98）」之列舉實際為8個唯一頁碼，因此新增 Audit Count Integrity。

## 二、正式升版

| 文件 | 前版 | 新版 | 新版 SHA-256 |
|---|---|---|---|
| 三AI共通協作規範 | V2.15 | V2.16 | `896594210b758a2b29a0c2848356808173ddf6a5644bd94b0e3d6fd231819d64` |
| ChatGPT Instructions | V3.23 | V3.24 | `a89e3ca73ef195701dd2f398804b5f696e059a0209815d197714ac7e39931b2d` |
| Gemini Instructions | V2.22 | V2.23 | `f058667eaa089185798333f66bfc5961c20354abaf479b9892e851f263a2984e` |
| Claude Instructions | V2.22 | V2.23 | `fb635b59559211f16def46fa82da64c4bd4cb57dc432f0a05240542e45d5dc16` |

## 三、核心新增

### Shared-Page Visual Audit Deduplication
同一 Batch 內相同 `來源檔＋頁碼` 只載入一次，可同時支援多材料獨立判定。

### Canonical Crop Reuse
同頁相同關鍵區域若已有足夠清晰裁切，本輪 Batch 直接重用；不同區域／清晰度不足才追加。

### Final Audit Visual Index
GPT 多材料 Final Audit 工作包新增：
`來源檔｜唯一頁碼｜涉及材料｜關鍵區域／圖名｜主要審核風險`

同一頁只列一次。

### Token Budget ≠ Scope Waiver
不得因 Token 成本而把 Final Audit 預設縮成「只查使用者點名項目」。

### Audit Count Integrity
若回報查核影像數量，使用：
`Unique Source Pages＝N｜Material-Page Links＝N｜Additional Crops＝N`

唯一頁數必須與實際頁碼清單一致。

## 四、品質不降級驗證

- Claude-FA Final Auditor only：保留
- 原圖重大錯讀：保留
- 必要條件漏／重：保留
- Candidate-ID／型號：保留
- P/F證據對稱：保留
- 跨型號／跨系列／跨證書：保留
- RFI／限制競爭：保留
- MCR：保留
- 重大 UNSUPPORTED：保留
- Gemini最多R2：保留
- Multi-Material Batch：保留
- Excel Executor＝GPT-W／ChatGPT Work：不變
- Excel工作頁／欄位／公式治理：未新增
- Reserve／Failover：不變

## 五、M36～M40示例

若關鍵頁為：
`45、46、80、81、82、96、97、98`

則：
`Unique Source Pages＝8`

若 p.80 同時涉及 M37／M38／M40：
- p.80 只載入一次；
- 同一必要區域裁切只建立一次；
- M37／M38／M40 技術判定仍各自完成。

## 六、同步驗證結論

`MD同步修正驗證＝PASS`

`治理內容狀態＝FORMAL SEALED`


## 七、Google Drive Formal Seal

2026-09-17 已完成治理本體上傳及 raw-byte 回讀。

| 文件 | Drive File-ID | Drive raw-byte SHA-256 | Seal |
|---|---|---|---|
| 三AI共通協作規範 V2.16 | `16rr_kT2paRuXaKhTIcyq_PaGBUvV0iPn` | `896594210b758a2b29a0c2848356808173ddf6a5644bd94b0e3d6fd231819d64` | PASS |
| ChatGPT Instructions V3.24 | `1WeyedlZQ_15odojpBmmtTHwK8xEt5yPl` | `a89e3ca73ef195701dd2f398804b5f696e059a0209815d197714ac7e39931b2d` | PASS |
| Gemini Instructions V2.23 | `1-fkK5WBqP8SgRkta9rrtZfnz1fQSO7pF` | `f058667eaa089185798333f66bfc5961c20354abaf479b9892e851f263a2984e` | PASS |
| Claude Instructions V2.23 | `1uZRErIgZNaVHdnZvUxUmHxVyxbUv_lum` | `fb635b59559211f16def46fa82da64c4bd4cb57dc432f0a05240542e45d5dc16` | PASS |

`Drive Seal＝PASS`

`最高正式治理版本＝共通 V2.16｜ChatGPT V3.24｜Gemini V2.23｜Claude V2.23｜同步驗證 V13`
