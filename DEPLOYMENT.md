# MD2PIC - Cloudflare Pages 部署指南

**最后更新**: 2026-06-01  
**版本**: v1.1.0

---

## 📦 当前部署结构

```
/workspace/
├── index.html          # 主应用 (单文件架构，v1.1.0)
├── README.md           # 项目说明
├── DEPLOYMENT.md       # 本文档
├── _headers            # Cloudflare Pages 安全头部
├── _redirects          # Cloudflare Pages 重定向规则
└── docs/               # 文档目录
    ├── ANALYSIS_REPORT.md
    ├── CODE_WIKI.md
    ├── FIXES_SUMMARY.md
    ├── PROJECT_STRUCTURE.md
    ├── ROADMAP.md
    ├── TEST_REPORT.md
    └── VERSION_v1.1.0.md
```

---

## 🚀 部署到 Cloudflare Pages（推荐）

### 方法 1: Git 仓库自动部署（推荐）

#### 1. 准备 Git 仓库
```bash
cd /path/to/your/project
git init
git add .
git commit -m "Initial commit - MD2PIC v1.1.0"
```

#### 2. 推送到 GitHub/GitLab
```bash
# 添加远程仓库
git remote add origin https://github.com/你的用户名/仓库名.git
git branch -M main
git push -u origin main
```

#### 3. 在 Cloudflare Pages 中创建项目
1. 访问 [Cloudflare Dashboard → Pages](https://dash.cloudflare.com/?to=/:account/pages)
2. 点击 **Create a project** → **Connect to Git**
3. 选择你的仓库
4. 配置构建设置：
   - **Framework preset**: `None (static)`
   - **Build command**: 留空
   - **Build output directory**: 留空（或 `/`）
5. 点击 **Save and Deploy**

---

### 方法 2: 直接上传（快速）

如果不想用 Git，可以直接上传文件：

1. 访问 Cloudflare Pages → Create a project → Direct Upload
2. 拖拽或选择项目文件夹
3. 等待部署完成

---

## 📋 Cloudflare Pages 配置建议

### 1. 自定义域名（可选但推荐）
在 Cloudflare Pages 项目设置中：
- 设置 → Custom domains
- 添加你的域名（如 md2pic.yourdomain.com）

### 2. 自动部署配置（Git 方式）
- Production branch: `main`（或你的主分支）
- 开启 **Preview deployments**（可选，用于 PR 预览）

### 3. 环境变量（当前不需要）
v1.1.0 不需要环境变量，纯静态应用。

---

## 🎨 当前架构说明与未来计划

### 现状（v1.1.0）
- **单文件架构**: 所有代码在 `index.html` 中
- **优点**: 部署简单，无依赖，零配置运行
- **缺点**: 代码维护较复杂，难以协作

### 未来（v2.0.0 计划）
根据 `PROJECT_STRUCTURE.md`，计划重构为：
```
src/
├── index.html
├── css/
│   ├── main.css
│   └── themes/
├── js/
│   ├── main.js
│   ├── database/
│   ├── editor/
│   └── ...
└── assets/
dist/  # 构建输出
```

**注意**: v2.0.0 重构会引入构建工具，部署方式会相应调整（需要构建步骤）。

---

## 🔍 验证部署

部署完成后，检查以下内容：
1. ✅ 首页正常加载
2. ✅ 所有功能正常工作
3. ✅ 文档可访问（/docs/README.md）
4. ✅ 无控制台错误

---

## 📊 性能优化建议（Cloudflare 特性）

### 1. 开启自动压缩
Cloudflare Pages 默认开启 Gzip/Brotli 压缩，无需额外配置。

### 2. 使用边缘缓存
我们已经配置了 `_headers` 文件，合理的缓存策略已设置。

### 3. 图片优化
如果未来添加本地图片资源，建议使用：
- WebP 格式
- 适当压缩
- CDN 加速（Cloudflare 自动处理）

---

## 🛠️ 部署故障排查

### 问题: 文档 404
**原因**: 文档在 `/docs/` 目录下，链接可能需要更新

**解决**: 在 README 中使用相对链接 `./docs/`

### 问题: 缓存过旧
**解决**: 在 Cloudflare Pages 项目中触发 **Purge Cache**

---

## 📌 总结

**当前部署建议**（v1.1.0）:
- ✅ 使用现有单文件架构，部署简单
- ✅ 添加了 `_headers` 增强安全性
- ✅ 文档已整理到 `docs/` 目录
- ✅ 可以直接部署到 Cloudflare Pages

**未来准备**（v2.0.0）:
- 等待模块化重构完成
- 将提供完整的 Vite 构建配置
- 部署流程会相应调整（需要构建步骤）

现在您可以直接将整个项目部署到 Cloudflare Pages 了！🚀
