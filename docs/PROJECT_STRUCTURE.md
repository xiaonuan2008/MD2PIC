# MD2PIC - 项目目录结构设计

**版本**: v1.0.0  
**最后更新**: 2026-06-01

---

## 📁 当前结构（v1.0 - 单文件版）

```
/workspace/
├── index.html                          # 主入口（1200+ 行，包含HTML/CSS/JS）
├── README.md                           # 项目说明
├── CODE_WIKI.md                        # 代码文档
├── TEST_REPORT.md                      # 测试报告
├── ANALYSIS_REPORT.md                  # 分析报告
├── FIXES_SUMMARY.md                    # 修复总结
├── ROADMAP.md                          # 迭代计划
└── PROJECT_STRUCTURE.md                # 本文档
```

---

## 📁 目标结构（v2.0 - 模块化重构版）

```
/workspace/
├── docs/                               # 文档目录
│   ├── CODE_WIKI.md
│   ├── TEST_REPORT.md
│   ├── ANALYSIS_REPORT.md
│   ├── FIXES_SUMMARY.md
│   ├── ROADMAP.md
│   └── PROJECT_STRUCTURE.md
├── src/                                # 源代码目录
│   ├── index.html                      # 主入口（只包含基础HTML结构）
│   ├── css/
│   │   ├── main.css                    # 主样式
│   │   ├── responsive.css              # 响应式样式
│   │   └── themes/                     # 主题样式
│   │       ├── default.css             # 小红书风
│   │       ├── minimal.css             # 极简高冷
│   │       ├── academic.css            # 学术严谨
│   │       └── cute.css                # 可爱手帐
│   ├── js/
│   │   ├── main.js                     # 入口脚本
│   │   ├── database/                   # 数据库模块
│   │   │   └── indexeddb.js
│   │   ├── editor/                     # 编辑器模块
│   │   │   ├── history.js              # 撤销/重做
│   │   │   └── toolbar.js              # 工具栏
│   │   ├── renderer/                   # 渲染模块
│   │   │   ├── markdown.js             # Markdown解析
│   │   │   └── pagination.js           # 分页引擎
│   │   ├── exporter/                   # 导出模块
│   │   │   ├── image.js                # 图片导出
│   │   │   └── zip.js                  # ZIP打包
│   │   ├── ui/                         # UI模块
│   │   │   ├── settings.js             # 设置面板
│   │   │   └── mobile.js               # 移动端适配
│   │   └── utils/                      # 工具函数
│   │       ├── dom.js
│   │       └── storage.js
│   └── assets/                         # 静态资源
│       └── fonts/                      # 本地字体（可选）
├── dist/                               # 构建输出目录
│   ├── index.html
│   ├── css/
│   ├── js/
│   └── assets/
├── tests/                              # 测试目录
│   ├── unit/                           # 单元测试
│   │   ├── database.test.js
│   │   ├── history.test.js
│   │   └── pagination.test.js
│   └── e2e/                            # E2E测试
│       └── export.test.js
├── scripts/                            # 脚本目录
│   ├── build.js                        # 构建脚本
│   └── dev-server.js                   # 开发服务器
├── .gitignore                          # Git忽略
├── package.json                        # 项目配置
├── vite.config.js                      # Vite配置
└── README.md                           # 项目说明
```

---

## 📁 中间阶段结构（v1.5 - 过渡版）

在完全重构之前，可以先拆分大文件：

```
/workspace/
├── index.html                          # 保持不变（v1.0兼容）
├── docs/                               # 文档（移动到单独文件夹）
│   ├── README.md
│   ├── CODE_WIKI.md
│   ├── TEST_REPORT.md
│   ├── ANALYSIS_REPORT.md
│   ├── FIXES_SUMMARY.md
│   ├── ROADMAP.md
│   └── PROJECT_STRUCTURE.md
├── v1.0/                               # v1.0存档
│   └── index.html
└── README.md                           # 根目录的快速入口
```

---

## 📋 重构优先级

### 阶段 1：文档整理（v1.1）
- [ ] 创建 `docs/` 文件夹
- [ ] 移动所有文档到 `docs/`
- [ ] 更新文档内的相对链接

### 阶段 2：样式拆分（v1.2）
- [ ] 提取 CSS 到独立文件
- [ ] 拆分主题到单独文件
- [ ] 拆分响应式样式

### 阶段 3：代码模块化（v1.3）
- [ ] 按功能拆分 JS 代码
- [ ] 使用 ES6 模块
- [ ] 添加简单的构建流程

### 阶段 4：完整重构（v2.0）
- [ ] 使用 Vite 构建工具
- [ ] 完整模块化架构
- [ ] 添加测试框架

---

## 📖 说明

### 为什么需要重构？
1. **可维护性**: 1200+ 行单文件难以维护
2. **可扩展性**: 新功能添加困难
3. **可测试性**: 无法进行单元测试
4. **协作**: 多人开发时容易冲突

### 保持 v1.0 兼容
- v1.0 的 `index.html` 将保留在仓库中
- 用户可以继续使用单文件版本
- 渐进式升级策略
