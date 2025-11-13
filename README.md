# 🎰 賽果可控轉盤元件 (Rigged Roulette Game)

一個使用 Vue 3、PixiJS 和 GSAP 製作的互動式轉盤遊戲元件，支援賽果控制功能。

![Demo](https://img.shields.io/badge/demo-live-success?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)

## 🌟 線上展示

[https://wjdesign.github.io/rigged-roulette-game/](https://wjdesign.github.io/rigged-roulette-game/)

## ✨ 特色功能

- 🎨 **精美動畫效果** - 使用 GSAP 實現流暢的旋轉動畫
- 🎯 **賽果可控** - 可預先設定或隨機選擇中獎結果
- 🖼️ **高性能渲染** - 使用 PixiJS WebGL 渲染引擎
- 📱 **響應式設計** - 支援各種螢幕尺寸（桌面、平板、手機）
- 🎭 **自訂獎項** - 可輕鬆配置獎項名稱、顏色和圖示
- ⚡ **TypeScript** - 完整的類型支援

## 🛠️ 技術棧

- **框架**: Vue 3 (Composition API)
- **語言**: TypeScript
- **圖形渲染**: PixiJS 8.x
- **動畫**: GSAP 3.x
- **建置工具**: Vite 7.x
- **樣式**: Sass
- **部署**: GitHub Pages + GitHub Actions

## 🚀 開發

```bash
# 啟動開發伺服器
npm run dev

# 開啟瀏覽器訪問
# http://localhost:5173
```

建置後的檔案會輸出到 `docs/` 目錄。

## 📐 專案結構

```
rigged-roulette-game/
├── src/
│   ├── App.vue                 # 主元件
│   ├── main.ts                 # 入口檔案
│   ├── style.css              # 全域樣式
│   └── common/
│       └── assets/
│           └── images/
│               └── roulette-game/  # 轉盤圖片資源
├── docs/                       # 建置輸出目錄（GitHub Pages）
├── .github/
│   └── workflows/
│       └── build.yml          # GitHub Actions 自動建置
├── vite.config.ts             # Vite 配置
├── tsconfig.json              # TypeScript 配置
└── package.json               # 專案依賴
```

## 🎮 使用方式

### 基本使用

轉盤元件會自動初始化並顯示。點擊中央的 "SPIN" 按鈕即可開始旋轉。

### 控制賽果

1. 使用右側的下拉選單選擇想要的結果
2. 點擊 "SPIN" 按鈕
3. 轉盤會旋轉並停在選定的結果上

### 隨機抽選

1. 在下拉選單中選擇「隨機抽選」
2. 點擊 "SPIN" 按鈕
3. 轉盤會隨機選擇一個結果

## ⚙️ 配置選項

在 `src/App.vue` 中可以自訂以下參數：

```typescript
// 轉盤尺寸
const ROULETTE_WIDTH = 400
const ROULETTE_HEIGHT = 400

// 動畫時間（秒）
const ROULETTE_DURATION_SECOND = 5

// 額外轉動圈數
const ROULETTE_EXTRA_ROUNDS = 10

// 獎項列表
const awardList = reactive<{ list: IAward[] }>({
  list: [
    {
      name: "$88",
      value: 1,
      color: "#fee393",
      textColor: "#ce0207",
      icon: "圖示路徑",
    },
    // ... 更多獎項
  ],
})
```

## 🚀 自動部署

本專案使用 GitHub Actions 自動建置和部署：

1. 推送程式碼到 `main` 分支
2. GitHub Actions 自動執行建置
3. 建置結果自動提交到 `docs/` 資料夾
4. GitHub Pages 自動發布更新

## 📝 授權

MIT License

## 👤 作者

**Wayne**

- GitHub: [@wjdesign](https://github.com/wjdesign)
- Blog: [Wayne's blog](https://wayne-blog.com/)

## 🙏 技術棧

- [Vue.js](https://vuejs.org/) - 漸進式 JavaScript 框架
- [PixiJS](https://pixijs.com/) - 高性能 2D WebGL 渲染引擎
- [GSAP](https://greensock.com/gsap/) - 專業級 JavaScript 動畫庫
- [Vite](https://vitejs.dev/) - 前端建置工具

---

⭐ 如果這個專案對您有幫助，歡迎給個 Star！
