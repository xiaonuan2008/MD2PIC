# MD2PIC - Code Wiki

## 项目概述

这是一个纯前端的图文排版工具，专为自媒体创作者设计，可将 Markdown 文本转换为精美的图片卡片，支持一键导出为单图或 ZIP 压缩包，适用于小红书等社交平台。

**版本号**: V15

---

## 目录结构

```
/workspace/
├── index.html        # 主入口文件，包含所有代码
├── README.md         # 项目说明文档
├── CODE_WIKI.md      # 本文件 - 代码wiki文档
└── TEST_REPORT.md    # 测试报告
```

---

## 技术栈

| 技术 | 用途 | 版本/来源 |
|------|------|----------|
| HTML5 | 页面结构 | - |
| CSS3 | 样式设计 | - |
| JavaScript (ES6+) | 核心逻辑 | - |
| [marked](https://marked.js.org/) | Markdown 解析 | CDN (最新) |
| [html2canvas](https://html2canvas.hertzen.com/) | 截图生成 | 1.4.1 |
| [JSZip](https://stuk.github.io/jszip/) | ZIP 打包 | 3.10.1 |
| [FileSaver.js](https://github.com/eligrey/FileSaver.js/) | 文件下载 | 2.0.5 |
| [KaTeX](https://katex.org/) | 数学公式渲染 | 0.16.9 |
| Google Fonts | 中/英文字体 | CDN |
| IndexedDB | 本地数据存储 | 浏览器原生 |

---

## 整体架构

```
┌─────────────────────────────────────────────────────────────────────────┐
│                               用户界面层                                 │
│  ┌──────────────────┐  ┌──────────────────────────────────────────┐   │
│  │  编辑器面板      │  │           预览面板                      │   │
│  │  - Markdown输入  │  │  ┌────────────────────────────────────┐ │   │
│  │  - 快捷工具栏    │  │  │     排版设置栏 (主题/字体/等)     │ │   │
│  │  - 文件操作      │  │  └────────────────────────────────────┘ │   │
│  └──────────────────┘  │  ┌────────────────────────────────────┐ │   │
│                        │  │       渲染的卡片列表             │ │   │
│  ┌──────────────────┐  │  └────────────────────────────────────┘ │   │
│  │  底部导航(Mobile)│  └──────────────────────────────────────────┘   │
│  └──────────────────┘                                               │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
┌─────────────────────────────────────────────────────────────────────────┐
│                              业务逻辑层                                 │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────────┐  │
│  │ 数据存储    │ │ 配置管理    │ │ 渲染引擎    │ │ 导出打包      │  │
│  │ (IndexedDB) │ │ (config)    │ │ (分页/Markdown)│ │ (html2canvas)│  │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    ↓
┌─────────────────────────────────────────────────────────────────────────┐
│                              外部依赖层                                 │
│  marked, html2canvas, JSZip, FileSaver, KaTeX, Google Fonts             │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 核心模块详解

### 1. IndexedDB 数据库引擎 (第 447-499 行)

**职责**: 本地持久化存储草稿、配置和图片数据。

**关键函数**:
- [`openDB()`](file:///workspace/index.html#L453-L464): 打开/创建数据库
- [`dbSet(storeName, key, value)`](file:///workspace/index.html#L466-L473): 存储数据
- [`dbGet(storeName, key)`](file:///workspace/index.html#L475-L483): 读取数据
- [`cleanupUnusedImages(currentText)`](file:///workspace/index.html#L485-L499): 清理未使用的图片

**数据库结构**:
```
MarkdownPaginatorDB (v1)
├── images (objectStore) - 存储上传的图片 (base64)
│   └── key: 'local_img_{timestamp}_{random}'
└── data (objectStore) - 存储配置和草稿
    ├── key: 'md_config' - 保存配置 JSON
    └── key: 'md_draft' - 保存草稿文本
```

---

### 2. 界面交互与配置模块 (第 501-774 行)

**职责**: 管理用户界面、配置项、历史记录等。

**配置项**:
| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| theme | 主题皮肤 | theme-default |
| font | 字体 | 'Noto Sans SC', sans-serif |
| textAlign | 文本对齐 | justify |
| listStyle | 列表样式 | default |
| ratio | 画布尺寸 | 900x1200 |
| bgColor | 背景色 | #ffffff |
| fontSize | 字号 | 22px |
| lineHeight | 行距 | 1.8 |
| coverMode | 封面模式 | true |
| watermark | 水印文字 | '' |

**关键函数**:
- [`switchTab(tab)`](file:///workspace/index.html#L548-L561): 移动端标签切换
- [`saveHistory(text)`](file:///workspace/index.html#L564-L574): 保存历史记录
- [`undo()`](file:///workspace/index.html#L576-L583): 撤销
- [`redo()`](file:///workspace/index.html#L585-L592): 重做
- [`insertMd(type)`](file:///workspace/index.html#L600-L656): 快捷插入 Markdown 语法
- [`saveConfigAndDraft()`](file:///workspace/index.html#L658-L671): 保存配置和草稿
- [`loadConfigAndDraft()`](file:///workspace/index.html#L673-L699): 加载配置和草稿
- [`adjustScale()`](file:///workspace/index.html#L765-L774): 调整预览缩放比例

---

### 3. 图片处理与渲染引擎 (第 776-904 行)

**职责**: 图片上传、Markdown 解析、渲染预览。

**关键函数**:
- [`processAndInsertImage(blob)`](file:///workspace/index.html#L780-L800): 处理并插入图片
- [`triggerRender()`](file:///workspace/index.html#L854-L904): 触发完整渲染流程
  - 步骤 1: Markdown 解析 (marked.parse)
  - 步骤 2: 解析后处理 (notice-box 高亮、任务列表)
  - 步骤 3: 异步加载图片
  - 步骤 4: 调用智能分页

---

### 4. 智能分页引擎 (第 906-1066 行)

**职责**: 将内容自动分页到多个卡片上。

**关键函数**:
- [`createNewPage(pageIndex)`](file:///workspace/index.html#L906-L933): 创建新页面
- [`paginateNodes()`](file:///workspace/index.html#L938-L1066): 核心分页算法
  - 自动检测内容溢出
  - 支持段落级二分法拆分文本
  - 支持 HR 标签强制分页
  - 封面模式支持

---

### 5. 打包导出功能 (第 1074-1205 页)

**职责**: 将渲染好的卡片导出为图片或 ZIP 包。

**关键函数**:
- [`exportImages()`](file:///workspace/index.html#L1077-L1205): 导出图片
  - 单页 → 直接下载 PNG
  - 多页 → 打包为 ZIP
  - 2x 高清缩放
  - 错误处理 (CORS, 超时等)

---

## 主题系统

项目包含 4 套预设主题：

| 主题 | 特点 | 适用场景 |
|------|------|----------|
| theme-default (小红书风) | 红色强调色、渐变背景 | 小红书、社交媒体 |
| theme-minimal (极简高冷) | 黑白配色、简洁设计 | 正式内容、文章 |
| theme-academic (学术严谨) | 深蓝色、学术风格 | 知识分享、教程 |
| theme-cute (可爱手帐) | 粉色/暖色调、活泼设计 | 生活分享、手帐 |

---

## 画布尺寸

| 比例 | 尺寸 | 适用场景 |
|------|------|----------|
| 3:4 | 900×1200 | 小红书长图 |
| 1:1 | 1080×1080 | 正方形卡片 |
| 9:16 | 1080×1920 | 全屏海报、短视频封面 |

---

## 特殊 Markdown 语法

### 1. 高亮提示框
```
>! 在这里输入高亮重点内容...
```

### 2. 任务列表
```
- [x] 已完成的任务
- [ ] 待办事项
```

### 3. 数学公式
```
$$ a^2 + b^2 = c^2 $$
```

### 4. 强制分页
```
---
```

---

## 样式变量

项目使用 CSS 变量，便于主题定制：

```css
:root {
  --page-width: 900px;
  --page-height: 1200px;
  --page-bg-color: #ffffff;
  --font-main: 'Noto Sans SC', sans-serif;
  --base-font-size: 22px;
  --base-line-height: 1.8;
  --text-align: justify;
  --theme-color: #ff4d4f;
  --highlight-color: rgba(255, 77, 79, 0.2);
}
```

---

## 响应式设计

### 桌面端 (≥ 900px)
- 分屏布局：左侧编辑区，右侧预览区
- 预览区顶部：设置栏网格布局
- 鼠标/键盘操作

### 移动端 (< 900px)
- 独占式布局，通过底部 Tab 切换
  - 写作区
  - 排版预览
- 设置栏：横向滑动排列
- 工具栏：横向滑动排列

---

## 依赖关系图

```
index.html
├── marked (CDN)
│   └── marked-katex-extension (CDN)
│       └── katex (CDN)
├── html2canvas (CDN)
├── JSZip (CDN)
├── FileSaver (CDN)
└── Google Fonts (CDN)
    ├── Noto Sans SC
    ├── Noto Serif SC
    ├── ZCOOL QingKe HuangYou
    ├── ZCOOL XiaoWei
    ├── ZCOOL QingKe NianZhi
    ├── ZCOOL YouShu
    ├── ZCOOL QingKe BenYuan
    ├── ZCOOL KaiShu
    ├── ZCOOL KuaiLe
    ├── Ma Shan Zheng
    ├── Anton
    ├── Caveat
    ├── Pacifico
    ├── Playfair Display
    └── Roboto
```

---

## 启动方式

**无需安装任何依赖！**

直接在浏览器中打开 [`index.html`](file:///workspace/index.html) 即可使用。

---

## 工作流程

1. **用户输入**: 在左侧编辑区输入或粘贴 Markdown 文本
2. **实时预览**: 右侧自动渲染并分页
3. **样式调整**: 在预览区顶部调整主题、字体、字号等
4. **导出**: 点击右上角「保存大图」或「打包 ZIP」

---

## 浏览器兼容性

- ✅ Chrome 60+
- ✅ Firefox 55+
- ✅ Safari 11+
- ✅ Edge 79+

需要支持:
- IndexedDB
- ES6+
- html2canvas 兼容
