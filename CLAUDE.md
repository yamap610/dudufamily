# Claude.md - 嘟嘟一家 Web App Suite

## 專案概況

**嘟嘟一家** 是一套家庭導向的 web app 套件，聚焦家庭管理與記憶保存。採用 **純 HTML + CDN 架構**，無建構流程，重點在功能實用性和家人共享體驗。

**核心理念**：簡潔、實用、家庭第一，保持開發輕量和快速迭代。

---

## 技術棧

### 前端框架
- **React 18**（CDN 加載，無 JSX 轉譯）
- **React DOM**（CDN）
- **Babel Standalone**（瀏覽器端 JSX 轉譯）
- **Tailwind CSS**（CDN，僅使用核心工具類）

### 後端 & 資料庫
- **Firebase Firestore**（實時資料庫）
- **Firebase Authentication**（可選，目前未廣泛使用）
- **Firebase SDK**（compat 版本，經由 `<script>` 標籤加載）

**為什麼是 compat SDK？** Safari 行動端對模組化 SDK 的相容性問題；`<script>` 標籤加載更穩定。

### 部署平台
- **GitHub Pages**（靜態檔案託管）
- **GitHub 帳號**：`yui0151` 和 `yamap610`

### 開發工具
- 純文字編輯器（無 Node.js、Webpack、建構工具）
- Git + GitHub（版本控制）
- 直接上傳 `index.html` 到 repo

---

## 現有應用

| App 名稱 | 用途 | URL | 部署位置 | 說明 |
|---------|------|-----|--------|------|
| **首頁儀表板** | 家庭天氣、待辦、紀念日、購物單 | `yui0151.github.io/home/` | yui0151 repo | 家庭日常核心樞紐，整合多功能 |
| Secretary（秘書） | 待辦事項、日程、家務管理 | `yui0151.github.io/secretary/` | yui0151 repo | 開發中 |
| Inventory（家庭清單） | 家庭物品清點、清單管理 | `yui0151.github.io/inventory/` | yui0151 repo | 開發中 |
| Wardrobe（衣櫥） | 衣物管理與搭配 | `yui0151.github.io/wardrobe/` | yui0151 repo | 開發中 |
| Pocket List（口袋清單） | 便簽、快速記錄 | `yamap610.github.io/pocket-list/` | yamap610 repo | 開發中 |
| M.Y Wiki（小百科） | 知識庫：育兒筆記、旅遊日誌、說明書 | `yamap610.github.io/mywiki/` | yamap610 repo | 開發中 |

**共享機制**：所有 app 都透過 **`roomCode`**（房間代碼）實現家人實時同步。輸入相同的 `roomCode`，即可看到同一個 Firestore 專案中的共享資料。

---

## 最新首頁儀表板設計

### 核心特性
- 🌙 登入畫面：輸入房間碼 (roomCode) 進入家庭共享區域
- 🌤️ 天氣卡片：實時顯示當地天氣溫度與建議
- 📋 待辦事項面板：快速新增、完成、刪除任務
- 📝 便籤區：黃色貼紙風格，支援快速記錄
- 🎂 紀念日追蹤：倒數日期、色碼警示（近期、中期、正常）
- 🛍️ 購物願望清單：追蹤想要的物品和購買狀態
- 🏠 應用導航：快速進入各個子應用（Secretary、Inventory、Wardrobe 等）
- 🚨 警報卡片：家庭重要通知、提醒事項

### UI 元件庫
```
登入畫面 (Login)
├─ 房間碼輸入框
├─ 進入按鈕
└─ 錯誤提示

應用首頁 (Dashboard)
├─ 頁頭（Logo + 同步狀態 + 房間碼顯示）
├─ 問候語（時段相應的問候）
├─ 天氣卡片（含溫度、天候圖案、穿衣建議）
├─ 警報卡片列表（高優先度提示）
├─ 應用網格（2 列，快速導航）
├─ 待辦面板（輸入框 + 任務列表）
├─ 便籤區（黃色貼紙、可新增編輯）
├─ 紀念日列表（倒數、展開收起）
└─ 購物願望清單（已買/未買分類）
```

### 資料結構
```javascript
// localStorage 本地快取 (單機用)
localStorage['dash_last_room'] = roomCode;

// 資料格式 (JSON)
{
  "roomCode": "ABC123",
  "todos": [
    { "id": "timestamp", "text": "任務文字", "done": false }
  ],
  "memos": [
    { "id": "timestamp", "text": "便籤內容" }
  ],
  "anniversaries": [
    { "id": "timestamp", "name": "紀念日名稱", "date": "YYYY-MM-DD", "icon": "🎂" }
  ],
  "wishes": [
    { "id": "timestamp", "text": "物品名稱", "note": "備註", "done": false }
  ],
  "alerts": [
    { "id": "timestamp", "title": "標題", "type": "urgent|warn|info", "icon": "emoji" }
  ]
}
```

---

## 設計系統

### 色彩方案（擴展）
```css
--milk: #fdf8f3;      /* 奶茶色 - 主背景 */
--cream: #f5ece0;     /* 淡奶油 - 輔助背景 */
--latte: #e8d5bc;     /* 拿鐵色 - 邊框 */
--moon: #c9a97a;      /* 月亮金 - 主強調 */
--moon2: #a07848;     /* 月亮金深 - 邊框、次強調 */
--brown: #8b6343;     /* 棕色 - 標題、文字 */
--deep: #5c3d1e;      /* 深棕 - 最深文字 */
--text: #3d2b1a;      /* 正文色 */
--muted: #a08060;     /* 灰棕 - 次級文字 */

/* 狀態色 */
--gs: #8aba88;        /* 綠色 - 成功 */
--gb: #eaf6e8;        /* 綠色背景 */
--rs: #d08888;        /* 紅色 - 緊急 */
--rb: #fceaea;        /* 紅色背景 */
--ys: #c8a050;        /* 黃色 - 警告 */
--yb: #fdf5e0;        /* 黃色背景 */
--bs: #7a9fc0;        /* 藍色 - 資訊 */
--bb: #eaf3fb;        /* 藍色背景 */
```

### 字體
- **正文/標題**：Noto Serif TC（思源宋體）
- **UI 控件**：Zen Kaku Gothic New（禪日式黑體）

### 設計原則
- 簡潔、親和
- 色彩克制（主要用奶茶和月亮金）
- 圓角、柔和的邊框（`--r: 14px`）
- 適配手機優先

---

## 強制性需求規範

### 1. 資料備份與匯出匯入
**所有 web app 都必須內建備份/還原功能**

每個 app 必須提供：
- **匯出功能**：將用戶資料匯出為 JSON 檔案，供下載
- **匯入功能**：從 JSON 檔案還原資料到 Firestore
- **UI 位置**：在設定或選單區域提供「備份」和「還原」按鈕
- **格式標準**：資料結構必須包含 roomCode，以便正確還原到共享家庭

### 2. 彈出視窗（Modal）的螢幕限制
**新增、編輯、刪除等彈出視窗必須完全適配手機螢幕**

強制規範：
- 視窗高度不得超過手機可見區域（考慮狀態列、虛擬鍵盤）
- 文字和表單不得被遮擋或溢出
- 內容可滾動，但視窗本身不可超出邊界
- 在 600px 以下螢幕寬度進行測試驗證
- 按鈕（確定、取消）永遠可見且易按

### 3. Emoji 自定義規範
**所有 emoji 由使用者自行輸入，不得由 code 自動插入**

原則：
- UI 中的視覺符號一律改用文字（「新增」、「編輯」、「刪除」）
- 若用戶要在內容中加 emoji，則由用戶複製貼上
- 避免色彩過艷或難以識別的 emoji
- 若涉及人物 emoji，膚色選擇限於最淡色選項

### 4. 人物 Emoji 膚色規範
**所有人物相關 emoji 一律採用最淡色（淺膚色如👨🏻）選項**

---

## 架構特徵

### 單檔案模式
每個 app 是一個獨立的 `index.html`，包含：
1. **CDN 引入**（React、Babel、Tailwind）
2. **Firestore 初始化代碼**（compat SDK）
3. **React 根組件**（在 `<script type="text/babel">` 中編寫）

### 資料同步
- **Firestore `onSnapshot`**：實時監聽文檔變化
- **`roomCode` 架構**：使用者輸入房間碼 → 綁定到同一 Firestore 集合
- **自動保存**：任何改動立即寫入 Firestore

### 存儲策略
- 無本地 localStorage（避免多裝置不同步）
- 全部靠 Firestore 作為單一信源

**已知限制**：
- `import.meta.env` 在純 HTML+CDN 中不可用（無法訪問環境變數）
- Firebase config 必須硬寫在 HTML 中或透過查詢參數傳遞
- 有時 Safari 行動端需額外相容性處理

---

## 開發工作流程

1. **本地編輯** → 直接在編輯器中修改 `index.html`
2. **本地測試** → 用瀏覽器或 Live Server 打開 HTML 檔案
3. **提交 Git** → `git add / commit / push` 到 GitHub
4. **自動部署** → GitHub Pages 自動發佈（無 CI/CD 流程）
5. **驗證線上** → 訪問對應的 GitHub Pages URL 檢查

---

## 快速參考：GitHub 帳號與部署

### yui0151 帳號
| App | Repo | 部署 URL | Raw 連結 |
|-----|------|---------|---------|
| Dashboard |`yui0151/home` | `https://yui0151.github.io/home/` | `https://raw.githubusercontent.com/yui0151/home/main/index.html` |
| Secretary | `yui0151/secretary` | `https://yui0151.github.io/secretary/` | `https://raw.githubusercontent.com/yui0151/secretary/main/index.html` |
| Inventory | `yui0151/inventory` | `https://yui0151.github.io/inventory/` | `https://raw.githubusercontent.com/yui0151/inventory/main/index.html` |
| Wardrobe | `yui0151/wardrobe` | `https://yui0151.github.io/wardrobe/` | `https://raw.githubusercontent.com/yui0151/wardrobe/main/index.html` |

### yamap610 帳號
| App | Repo | 部署 URL | Raw 連結 |
|-----|------|---------|---------|
| Pocket List | `yamap610/pocket-list` | `https://yamap610.github.io/pocket-list/` | `https://raw.githubusercontent.com/yamap610/pocket-list/main/index.html` |
| M.Y Wiki | `yamap610/mywiki` | `https://yamap610.github.io/mywiki/` | `https://raw.githubusercontent.com/yamap610/mywiki/main/index.html` |

---

## 新功能設計建議

### 組織
- 功能按「房間」（roomCode）隔離，方便多家庭共存

### 技術決策
- **優先**：純 HTML + Firestore
- **次選**：簡單的 Tailwind 樣式（不用 @apply、自訂 CSS）
- **避免**：外部複雜庫、Node.js 工具、建構工具

### 更新與迭代
- 保持向後相容（roomCode 結構不破壞）
- 新功能最好作為新集合或新欄位，不改既有資料結構
- 如需大改動，先在測試 repo 驗證

---

## 關鍵代碼片段參考

### Firestore 初始化（compat SDK）
```html
<script src="https://www.gstatic.com/firebasejs/9.x.x/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.x.x/firebase-firestore-compat.js"></script>

<script>
const firebaseConfig = {
  apiKey: "YOUR_KEY",
  projectId: "your-project",
  // ...
};
firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();
</script>
```

### roomCode 綁定與實時監聽
```javascript
const [roomCode, setRoomCode] = useState("");
const [docs, setDocs] = useState([]);

useEffect(() => {
  if (!roomCode) return;
  
  const unsubscribe = db.collection("docs")
    .where("roomCode", "==", roomCode)
    .onSnapshot((snapshot) => {
      setDocs(snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
    });
  
  return () => unsubscribe();
}, [roomCode]);
```

### 新增文檔
```javascript
const addDoc = async (data) => {
  await db.collection("docs").add({
    ...data,
    roomCode,
    createdAt: new Date(),
  });
};
```

### 備份與還原（範例）
```javascript
// 匯出為 JSON
const exportBackup = async () => {
  const snapshot = await db.collection("docs")
    .where("roomCode", "==", roomCode)
    .get();
  
  const data = snapshot.docs.map(doc => doc.data());
  const json = JSON.stringify(data, null, 2);
  
  // 建立下載
  const blob = new Blob([json], { type: "application/json" });
  const url = URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = `backup-${roomCode}-${Date.now()}.json`;
  a.click();
};

// 匯入 JSON
const importBackup = async (file) => {
  const text = await file.text();
  const data = JSON.parse(text);
  
  for (const item of data) {
    await db.collection("docs").add(item);
  }
};
```

---

## 已知問題 & 解決方案

| 問題 | 原因 | 解決方案 |
|------|------|--------|
| Safari 行動端樣式異常 | CSS 相容性或 JS 執行延遲 | 在 CDN 加載後延遲初始化（`setTimeout`） |
| Firestore 安全規則報錯 | 新文檔結構未授權 | 更新 Firestore 規則，允許相應集合的讀寫 |
| `import.meta.env` 未定義 | 純 HTML 無法訪問 | 改用查詢參數或硬寫設定 |
| GitHub Pages 快取過期 | 瀏覽器快取 | 用 `?v=timestamp` 加版本號強制重新整理 |
| 彈出視窗超出手機螢幕 | CSS 高度設定不當 | 使用 `max-height: 80vh` 加 `overflow-y: auto` |

---

**最後更新**：2026-03-31  
**維護人**：由依（yui0151 & yamap610）
