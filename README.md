# HoHoStay 訂房平台 - 前端專案

Vue 3 開發的前端應用程式

## 環境需求

- Node.js >= 20.19.0 或 >= 22.12.0
- npm 或 yarn

## 快速開始

### 1. 安裝依賴

```sh
npm install
```

### 2. 設置環境變數

複製 `.env.example` 為 `.env` 並填入必要的值：

```sh
# Windows
copy .env.example .env

# Mac/Linux
cp .env.example .env
```

**必須設置的環境變數：**

- `VITE_GOOGLE_MAPS_API_KEY`：Google Maps API Key（必須）
  - 申請方式：[Google Cloud Console](https://console.cloud.google.com/apis/credentials)
  - 需要啟用以下 API：Maps JavaScript API、Places API

**可選的環境變數（有預設值）：**

- `VITE_API_BASE_URL`：後端 API 基礎網址（預設：`http://localhost:8080/api`）
- `VITE_FILE_BASE_URL`：檔案基礎網址（預設：`http://localhost:8080`）
- `VITE_PHOTO_URL`：照片 URL（預設：`http://localhost:8080/images`）
- `VITE_PROFILE_PHOTO_URL`：個人資料照片 URL（預設：`http://localhost:8080/images`）

### 3. 啟動開發伺服器

```sh
npm run dev
```

開發伺服器會啟動在 `http://localhost:5173`

### 4. 編譯生產版本

```sh
npm run build
```

## 專案結構

- `src/user/` - 使用者前台
- `src/admin/` - 管理員後台
- `src/administrant/` - 管理者後台

## 推薦的開發工具

### IDE

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (請停用 Vetur)

### 瀏覽器擴充功能

- Chromium 瀏覽器（Chrome, Edge, Brave 等）:
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)

## 注意事項

- 環境變數檔案（`.env`）不會被提交到 Git，請確保有設置 `.env` 檔案
- 如果沒有設置 `VITE_GOOGLE_MAPS_API_KEY`，地圖相關功能將無法使用
- 確保後端服務正在運行（預設：`http://localhost:8080`）

## 更多資訊

詳細配置說明請參考 [Vite 官方文檔](https://vite.dev/config/)
