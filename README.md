# 臺灣生命教育的哲學基礎與發展歷程

孫效智教授｜114-2 學期簡報

線上瀏覽：<https://js-1105.github.io/life-edu-philosophy/>

## 內容範圍

本簡報依據〈臺灣生命教育的哲學基礎與發展歷程〉（孫效智，2026.05.22）一文 P.1–P.18 製作，涵蓋：

| 段落 | 主題 |
|---|---|
| 暖身① | 為什麼需要生命教育？（衛福部 2024 國人死因統計、兒福聯盟 2025 心理健康調查） |
| 暖身② | 你認為人生最重要的三件事是什麼？（即時文字雲） |
| 壹 | 推動背景——教育本質的失落與重新召喚 |
| 貳 | 以「人生 X 問」探討人生最重要課題（卡繆、托爾斯泰、康德、高更） |
| 參 | 臺灣生命教育的人生三問（終極關懷、價值思辨、靈性修養） |
| 肆 | 五大核心素養（含表 1） |

不含原文「伍、臺灣生命教育的發展歷程」（P.19 起）。

## 操作

| 按鍵 | 功能 |
|---|---|
| ← → 或畫面左右半邊點擊 | 切換頁面 |
| Space / PageDown | 下一頁 |
| F | 全螢幕 |
| O | 文件模式（垂直滾動） |
| D | 暖身文字雲：載入示範資料（預演用） |
| R | 暖身文字雲：清空**本地**顯示（不影響 Firebase） |
| **Shift + C** | **清空 Firebase 上所有真實資料**（會跳確認對話框） |
| Home / End | 第一頁／最後一頁 |

## 文字雲資料管理

### 課前準備：清空舊資料

開課前若有上一場留下的舊資料，**兩個方法擇一**：

#### 方法 A：簡報內按 Shift + C（推薦，需先更新 Firebase 規則一次，見下文）
1. 切到 S8 文字雲頁
2. 按 **Shift + C**
3. 出現「確定要清空所有文字雲資料？」對話框，按「確定」
4. 約 1 秒內，文字雲清空、計數歸零

#### 方法 B：Firebase Console 手動刪除（即刻可用，無需設定）
1. 開啟 <https://console.firebase.google.com/project/afl-slide-interactivity/database/afl-slide-interactivity-default-rtdb/data>
2. 展開左側 `polls` → `life`
3. **將滑鼠移到 `votes` 節點上**，右側會出現 ✕ 圖示
4. 點 ✕ → 確認刪除
5. 回到簡報硬重新整理，文字雲已清空

### 啟用方法 A 必需的規則更新（5 秒）

預設規則禁止 `.remove()`，需放寬一次：

1. 開啟 <https://console.firebase.google.com/project/afl-slide-interactivity/database/afl-slide-interactivity-default-rtdb/rules>
2. 把整段規則替換為下方：

```json
{
  "rules": {
    "polls": {
      "$pollId": {
        ".read": true,
        ".write": "auth != null",
        "votes": {
          "$voteId": {
            ".validate": "newData.hasChildren(['text','ts']) && newData.child('text').isString() && newData.child('text').val().length <= 30 && newData.child('text').val().length >= 1"
          }
        }
      }
    }
  }
}
```

3. 按右上「**發布**」
4. 之後 Shift+C 即可一鍵清空

> ⚠️ 此版規則允許任何匿名使用者刪除資料（不只新增）。風險低：學生不知道後台 URL，但理論上能寫程式刪除。若課程涉及敏感資料，建議用方法 B。

封面有「進入全螢幕．開始演講」按鈕，演講時建議由此進入。

## 文字雲：示範模式 vs 真實互動模式

### 現況：示範模式（預設）

無需後端，立即可用。`vote.html` 的輸入暫存於瀏覽器 `sessionStorage`，主畫面按 `D` 鍵載入內建示範詞彙（健康、家人、愛、自由、夢想⋯⋯）。

### 升級：Firebase 即時模式

讓觀眾掃 QR → 在手機填寫 → 主畫面文字雲即時更新。設定步驟：

1. **建立 Firebase 專案**
   - 前往 <https://console.firebase.google.com> → 建立專案
   - 名稱建議：`afl-slide-interactivity`
   - 關閉 Google Analytics（用不到）

2. **啟用 Realtime Database**
   - 左側選單 → Realtime Database → 建立資料庫
   - 位置選 `asia-southeast1`（新加坡）
   - 初次選「測試模式」，30 天內務必改為下方安全規則

3. **啟用匿名登入**
   - Authentication → 登入方式 → 匿名 → 啟用

4. **取得 `firebaseConfig`**
   - 專案設定 → 新增網頁應用程式（`</>`）
   - 複製 `firebaseConfig` 物件

5. **套用安全規則**（Realtime Database → 規則）
   ```json
   {
     "rules": {
       "polls": {
         "$pollId": {
           ".read": true,
           "votes": {
             ".write": "auth != null && newData.exists()",
             "$voteId": {
               ".validate": "newData.hasChildren(['text','ts']) && newData.child('text').isString() && newData.child('text').val().length <= 30 && newData.child('text').val().length >= 1"
             }
           }
         }
       }
     }
   }
   ```

6. **填入兩處設定並啟用**
   - `index.html`：將 `const USE_FIREBASE = false;` 改為 `true`，並在 `firebaseConfig` 物件填入金鑰
   - `vote.html`：同上

7. **重新 push** 到 GitHub，等待 GitHub Pages 部署完成（約 1 分鐘）即可使用

### QR 碼

`qr_life.svg` 已產生，指向 `https://js-1105.github.io/life-edu-philosophy/vote.html?p=life`。若日後變更網址，重新執行：

```bash
python3 -c "
import qrcode, qrcode.image.svg
qr = qrcode.QRCode(error_correction=qrcode.constants.ERROR_CORRECT_M, box_size=10, border=2)
qr.add_data('https://js-1105.github.io/life-edu-philosophy/vote.html?p=life')
qr.make(fit=True)
qr.make_image(image_factory=qrcode.image.svg.SvgPathImage).save('qr_life.svg')
"
```

## 引用來源

主要依據孫效智〈臺灣生命教育的哲學基礎與發展歷程〉一文，並於簡報「引用來源」頁列出延伸文獻。暖身數據出處：

- 衛生福利部統計處（2024）。《2024 年國人死因統計》。
- 兒福聯盟（2025）。《臺灣青少年心理健康調查報告》。n=7,007。

## 檔案結構

```
life-edu-philosophy/
├── index.html      # 主簡報（34 張投影片）
├── vote.html       # 觀眾填寫頁（手機掃 QR 開啟）
├── qr_life.svg     # 指向 vote.html 的 QR 碼
└── README.md       # 本說明文件
```
